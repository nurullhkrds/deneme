import java.math.BigDecimal;
import java.time.LocalDate;
import java.time.YearMonth;
import java.time.format.DateTimeFormatter;
import java.time.format.DateTimeParseException;
import java.time.format.ResolverStyle;
import java.util.ArrayList;
import java.util.Collection;
import java.util.List;
import java.util.Map;

@StanConfig(paymentStanRequired = true, paymentStanColumn = EnumHarmoniTableColumn.INFO_2)
public class TahsilatOyakIntegration extends AbstractIntegration implements BillIntegration, ReconcilationIntegration {

    private static final DateTimeFormatter DATE_FORMATTER = DateTimeFormatter.ofPattern("dd/MM/yyyy");
    private static final DateTimeFormatter INPUT = DateTimeFormatter.ofPattern("dd/MM/uuuu").withResolverStyle(ResolverStyle.STRICT);
    private static final DateTimeFormatter OUTPUT = DateTimeFormatter.ofPattern("yyyyMMdd");

    // --- ENUMS ---

    /** Sabit borç tipi kodları (info5 / bazı senaryolarda billNo için) */
    private enum DebtType {
        EMS_PAY_INCREASE("041"),
        EMS_RESERVE_INCREASE("042"),
        BORC_VERME("13"),
        KONUT_KREDISI("22"),
        KOBF("025"),
        DIGER("999");

        private final String code;
        DebtType(String code) { this.code = code; }
        public String code() { return code; }
    }

    /** info1 tarafındaki metinlerin standartlaşması için */
    private enum PaymentDesc {
        BORC_VERME_TAKSIT("Borc Verme Taksit Odemesi"),
        KONUT_KREDISI_TAKSIT("Konut Kredisi Taksit Odemesi"),
        FEVKALADE_TAKSIT("Fevkalade Taksit Odemesi"),
        EMS_PAY_ARTIRIM("Ems Pay Artirim Odemesi"),
        EMS_REZERV_ARTIRIM("Ems Emsal Tamamlama Rezerv Artirim Odemesi"),
        KOBF_AIDAT("KOBF Aidat Odemesi"),
        DIGER_ODEMELER("Diğer Odemeler");

        private final String text;
        PaymentDesc(String text) { this.text = text; }
        public String text() { return text; }
    }

    /** info1’in sol tarafında hangi değerin prefix olacağını belirtir */
    private enum Info1PrefixType {
        TAHSILAT_TURU,   // plan.getTahsilatTuru()
        DEBT_TYPE_CODE   // sabit borç tipi kodu (ör. 13, 22, 041 vb.)
    }

    @Override
    public QuerySubscriberResponse querySubscriber(QuerySubscriberRequest remoteRequest) {
        return null;
    }

    @Override
    public QueryBillsResponse queryBills(QueryBillsRequest remoteRequest) {
        QueryBillsResponse response = new QueryBillsResponse();
        List<BaseBillDTO> billDTOS = new ArrayList<>();

        OyakUyeBorcDurumunuTespitEtRequest institutionRequest = new OyakUyeBorcDurumunuTespitEtRequest();
        institutionRequest.setKullaniciAdi(getUserName());
        institutionRequest.setSifre(getPword());
        institutionRequest.setKun(remoteRequest.getSubscriberNumber1());

        OyakResponse institutionResponse = getService().uyeBorcDurumunuTespitEt(institutionRequest);

        if (!isSuccess(institutionResponse.getHataMap())) {
            Map.Entry<String, String> firstErr = firstEntry(institutionResponse.getHataMap());
            if (firstErr != null) {
                resolveResponseMessage(firstErr.getKey(), response);
                response.setInstitutionResultDetail(firstErr.getValue());
            } else {
                resolveResponseMessage("UNKNOWN", response);
                response.setInstitutionResultDetail("Bilinmeyen hata");
            }
            return response;
        }

        // Başarılı durum
        resolveResponseMessage("0000", response);
        String institutionResultMessage = "Basarili";

        if (BillPaymentsConsts.RESPONSE_STATUS.SUCCESS.getValue().equals(response.getStatus())) {

            // 1) Borç Verme Hizmeti – plan listesi
            addBillsFromPlans(
                institutionResponse.getBorcVermeHizmetiOdemePlaniDTOs(),
                remoteRequest,
                institutionResponse,
                DebtType.BORC_VERME.code(),
                PaymentDesc.BORC_VERME_TAKSIT,
                Info1PrefixType.TAHSILAT_TURU,
                billDTOS
            );

            // 2) Konut Kredisi – plan listesi
            addBillsFromPlans(
                institutionResponse.getKonutKredisiOdemePlaniDTOs(),
                remoteRequest,
                institutionResponse,
                DebtType.KONUT_KREDISI.code(),
                PaymentDesc.KONUT_KREDISI_TAKSIT,
                Info1PrefixType.TAHSILAT_TURU,
                billDTOS
            );

            // 3) Fevkalade Borç Verme Hizmeti – plan listesi (kod dinamik: tahsilatTuru)
            List<OdemePlaniDTO> fevkaladeList = institutionResponse.getFevkaladeBorcVermeHizmetiOdemePlaniDTOs();
            if (isNotEmpty(fevkaladeList)) {
                for (OdemePlaniDTO plan : fevkaladeList) {
                    String dynamicCode = String.valueOf(plan.getTahsilatTuru()); // debtType = tahsilatTuru
                    BaseBillDTO dto = billFromPlan(
                        remoteRequest,
                        institutionResponse,
                        plan,
                        dynamicCode,
                        PaymentDesc.FEVKALADE_TAKSIT,
                        String.valueOf(plan.getTahsilatTuru())
                    );
                    dto.setBillDueDate(getLastDateOfMonth(plan.getYil(), plan.getAy()));
                    billDTOS.add(dto);
                }
            }

            // 4) EMS rezerv / pay arttırım
            if (institutionResponse.isEmsUyesiMi() && institutionResponse.getEmsRezervOdemeleriDTO() != null) {
                EmsRezervOdemeleriDTO ems = institutionResponse.getEmsRezervOdemeleriDTO();

                if (ems.getBirinciPayArtirimTutari() != null) {
                    billDTOS.add(createEmsShareIncreaseBill(remoteRequest, institutionResponse, ems.getBirinciPayArtirimTutari()));
                }
                if (ems.getIkinciPayArtirimTutari() != null) {
                    billDTOS.add(createEmsShareIncreaseBill(remoteRequest, institutionResponse, ems.getIkinciPayArtirimTutari()));
                }
                if (ems.getUcuncuPayArtirimTutari() != null) {
                    billDTOS.add(createEmsShareIncreaseBill(remoteRequest, institutionResponse, ems.getUcuncuPayArtirimTutari()));
                }
                if (ems.isRezervArtirimBilgisiVarMi()) {
                    billDTOS.add(createEmsReserveIncreaseBill(remoteRequest, institutionResponse));
                }
            }

            // 5) İstenirse 999-Diğer ödeme (her durumda)
            // billDTOS.add(createOtherBill(remoteRequest, institutionResponse));

            response.setBills(billDTOS);
            response.setInstitutionResultDetail(institutionResultMessage);
            setRemoteResponseData(
                response,
                remoteRequest,
                PYMLogUtil.convertObjectToJsonString(institutionRequest),
                PYMLogUtil.convertObjectToJsonString(response)
            );
        }

        return response;
    }

    // ----------------- HELPERS (tekrarları toplayan metotlar) -----------------

    private void addBillsFromPlans(
        List<OdemePlaniDTO> plans,
        QueryBillsRequest req,
        OyakResponse inst,
        String debtTypeCode,
        PaymentDesc desc,
        Info1PrefixType prefixType,
        List<BaseBillDTO> out
    ) {
        if (!isNotEmpty(plans)) return;

        for (OdemePlaniDTO plan : plans) {
            String prefix;
            if (prefixType == Info1PrefixType.TAHSILAT_TURU) {
                prefix = String.valueOf(plan.getTahsilatTuru());
            } else {
                prefix = debtTypeCode;
            }

            BaseBillDTO dto = billFromPlan(req, inst, plan, debtTypeCode, desc, prefix);
            dto.setBillDueDate(getLastDateOfMonth(plan.getYil(), plan.getAy()));
            out.add(dto);
        }
    }

    private BaseBillDTO billFromPlan(
        QueryBillsRequest req,
        OyakResponse inst,
        OdemePlaniDTO plan,
        String debtTypeCode,
        PaymentDesc desc,
        String info1Prefix
    ) {
        BaseBillDTO dto = new BaseBillDTO();
        fillCommonFields(dto, req, inst);

        dto.setBillNo(createBillNo(null, req.getSubscriberNumber1(), debtTypeCode, plan));
        dto.setInfo1(composeInfo1(info1Prefix, desc));
        dto.setInfo3(plan.getYil() + "-" + plan.getAy());
        dto.setInfo4(plan.getKrediId());
        dto.setInfo5(debtTypeCode);
        dto.setBillAmount(BigDecimal.valueOf(plan.getTutar()));
        dto.setIsDeclarationPayable(false);
        dto.setIsAccountingDescription(false);

        return dto;
    }

    private String composeInfo1(String prefix, PaymentDesc desc) {
        return prefix + "-" + desc.text();
    }

    private Map.Entry<String, String> firstEntry(Map<String, String> map) {
        if (map == null || map.isEmpty()) return null;
        for (Map.Entry<String, String> e : map.entrySet()) {
            return e;
        }
        return null;
    }

    private boolean isNotEmpty(Collection<?> c) {
        return c != null && !c.isEmpty();
    }

    // ----------------- Var olan metotlar (küçük dokunuşlarla) -----------------

    private void fillCommonFields(BaseBillDTO billDTO, QueryBillsRequest req, OyakResponse inst) {
        billDTO.setProduct(req.getProduct());
        billDTO.setInstitution(req.getInstitution());
        billDTO.setSubscriberNo(req.getMergedSubscriberNumber());
        billDTO.setBillIssueDate(LocalDate.now());
        String ad = inst != null ? inst.getAdi() : null;
        String soyad = inst != null ? inst.getSoyadi() : null;
        billDTO.setSubscriberName((ad != null ? ad : "") + (soyad != null ? soyad : ""));
        billDTO.setInstitutionServiceType(req.getInstitutionServiceType());
        billDTO.setCurrency(req.getCurrencyCode());
    }

    private BaseBillDTO createEmsKOBFBill(QueryBillsRequest req, OyakResponse inst) {
        BaseBillDTO dto = new BaseBillDTO();
        fillCommonFields(dto, req, inst);
        dto.setBillNo("sequencegelecek"); // sequence
        dto.setBillAmount(BigDecimal.ZERO);
        dto.setBillDueDate(LocalDate.now());
        dto.setInfo1(composeInfo1(DebtType.KOBF.code(), PaymentDesc.KOBF_AIDAT));
        dto.setInfo3(LocalDate.now().toString());
        dto.setInfo5(DebtType.KOBF.code());
        dto.setIsDeclarationPayable(true);
        dto.setIsAccountingDescription(false);
        return dto;
    }

    private BaseBillDTO createEmsShareIncreaseBill(QueryBillsRequest req, OyakResponse inst, String paidAmount) {
        BaseBillDTO dto = new BaseBillDTO();
        fillCommonFields(dto, req, inst);
        String billDueDate = inst.getEmsRezervOdemeleriDTO().getPayArtirimGecerlilikTarihi();

        dto.setBillNo(createBillNo(billDueDate, req.getSubscriberNumber1(), DebtType.EMS_PAY_INCREASE.code(), null));
        dto.setBillAmount(toBigDecimal(paidAmount));
        dto.setBillDueDate(parseToLocalDate(billDueDate));
        dto.setInfo1(composeInfo1(DebtType.EMS_PAY_INCREASE.code(), PaymentDesc.EMS_PAY_ARTIRIM));
        dto.setInfo3(billDueDate);
        dto.setInfo5(DebtType.EMS_PAY_INCREASE.code());
        dto.setIsDeclarationPayable(true);
        dto.setIsAccountingDescription(false);
        return dto;
    }

    private BaseBillDTO createEmsReserveIncreaseBill(QueryBillsRequest req, OyakResponse inst) {
        BaseBillDTO dto = new BaseBillDTO();
        fillCommonFields(dto, req, inst);

        dto.setBillNo(createBillNo(null, req.getSubscriberNumber1(), DebtType.EMS_RESERVE_INCREASE.code(), null));
        dto.setBillAmount(BigDecimal.ZERO);
        dto.setBillDueDate(LocalDate.now());
        dto.setInfo1(composeInfo1(DebtType.EMS_RESERVE_INCREASE.code(), PaymentDesc.EMS_REZERV_ARTIRIM));
        dto.setInfo3(LocalDate.now().toString());
        dto.setInfo5(DebtType.EMS_RESERVE_INCREASE.code());
        dto.setIsDeclarationPayable(true);
        dto.setIsAccountingDescription(false);

        String minLimit = inst.getEmsRezervOdemeleriDTO().getRezervArtirimAltLimit();
        String maxLimit = inst.getEmsRezervOdemeleriDTO().getRezervArtirimUstLimit();
        dto.setMinPaymentAmount(toBigDecimal(minLimit));
        dto.setMaxPaymentAmount(toBigDecimal(maxLimit));
        return dto;
    }

    private BaseBillDTO createOtherBill(QueryBillsRequest req, OyakResponse inst) {
        BaseBillDTO dto = new BaseBillDTO();
        fillCommonFields(dto, req, inst);
        dto.setBillNo("sequencegelecek");
        dto.setBillAmount(BigDecimal.ZERO);
        dto.setBillDueDate(LocalDate.now());
        dto.setInfo1(composeInfo1(DebtType.DIGER.code(), PaymentDesc.DIGER_ODEMELER));
        dto.setInfo3(LocalDate.now().toString());
        dto.setInfo5(DebtType.DIGER.code());
        dto.setIsDeclarationPayable(true);
        dto.setIsAccountingDescription(true);
        return dto;
    }

    private BigDecimal toBigDecimal(String value) {
        if (value == null || value.trim().isEmpty()) {
            return null;
        }
        try {
            String normalized = value.replace(",", "").trim();
            return new BigDecimal(normalized);
        } catch (NumberFormatException e) {
            throw new IllegalArgumentException("Invalid Number format:" + value, e);
        }
    }

    private LocalDate getLastDateOfMonth(int year, int month) {
        if (year <= 0) {
            throw new IllegalArgumentException("Yıl 0 veya negatif olamaz: " + year);
        }
        if (month < 1 || month > 12) {
            throw new IllegalArgumentException("Ay 1 ile 12 arasında olmalı: " + month);
        }
        YearMonth yearMonth = YearMonth.of(year, month);
        return yearMonth.atEndOfMonth();
    }

    private LocalDate parseToLocalDate(String dateStr) {
        if (dateStr == null || dateStr.isBlank()) {
            return null;
        }
        try {
            return LocalDate.parse(dateStr, DATE_FORMATTER);
        } catch (DateTimeParseException e) {
            return null;
        }
    }

    private String createBillNo(String billDueDate,
                                String subscriberNo,
                                String debtType,
                                OdemePlaniDTO odemePlan) {

        String today = LocalDate.now().format(OUTPUT);

        if (DebtType.EMS_PAY_INCREASE.code().equals(debtType)) {
            String formattedDue = formatDueDate(billDueDate);
            String aboneno = requireValue(subscriberNo, "Abone no");
            return formattedDue + aboneno + today;

        } else if (DebtType.EMS_RESERVE_INCREASE.code().equals(debtType)) {
            String aboneno = requireValue(subscriberNo, "Abone no");
            return today + aboneno + DebtType.EMS_RESERVE_INCREASE.code();

        } else if (DebtType.BORC_VERME.code().equals(debtType)
                || DebtType.KONUT_KREDISI.code().equals(debtType)
                || "14".equals(debtType)) { // olası diğer kod
            requireNotNull(odemePlan, "Ödeme planı");
            return odemePlan.getKrediId() + odemePlan.getYil() + odemePlan.getAy() + odemePlan.getTahsilatTuru();

        } else {
            // Fevkalade gibi “dinamik kod” senaryosunda debtType zaten tahsilatTuru olarak geliyor olabilir
            if (odemePlan != null) {
                return odemePlan.getKrediId() + odemePlan.getYil() + odemePlan.getAy() + odemePlan.getTahsilatTuru();
            }
            throw new IllegalArgumentException("Geçersiz debtType: " + debtType);
        }
    }

    private String formatDueDate(String date) {
        String value = requireValue(date, "Son ödeme tarihi");
        LocalDate parsed = LocalDate.parse(value, INPUT);
        return parsed.format(OUTPUT);
    }

    private String requireValue(String val, String fieldName) {
        if (val == null || val.trim().isEmpty()) {
            throw new IllegalArgumentException(fieldName + " boş olamaz");
        }
        return val.trim();
    }

    private void requireNotNull(Object obj, String fieldName) {
        if (obj == null) {
            throw new IllegalArgumentException(fieldName + " boş olamaz");
        }
    }

    private boolean isSuccess(Map<String, String> errorCode) {
        return errorCode == null || errorCode.isEmpty();
    }
}
