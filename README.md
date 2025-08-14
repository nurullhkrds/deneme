@Override
public QueryBillsResponse queryBills(QueryBillsRequest remoteRequest) {
    QueryBillsResponse response = new QueryBillsResponse();
    List<BaseBillIDTO> billDTOS = new ArrayList<BaseBillIDTO>();

    // 0) Kurum servisini çağır: institutionResponse al
    OyakUyeBorcDurumunuTespitEtRequest institutionRequest = new OyakUyeBorcDurumunuTespitEtRequest();
    institutionRequest.setKullaniciAdi(getUserName());
    institutionRequest.setSifre(getPword());                       // görselde böyleydi
    institutionRequest.setTckn(remoteRequest.getSubscriberNumber1());

    OyakResponse institutionResponse = getService().uyeBorcDurumunuTespitEt(institutionRequest);
    if (institutionResponse == null) {
        response.setBills(billDTOS);
        return response;
    }

    // 1) Borç Verme Hizmet Ödeme Planları
    OdemePlaniDTOsWrapper hizmetWrp = institutionResponse.getBorcVermeHizmetOdemePlaniDTOs();
    if (hasPlans(hizmetWrp)) {
        for (OdemePlaniIDTO plan : plansOf(hizmetWrp)) {
            BaseBillIDTO dto = new BaseBillIDTO();
            fillCommonFields(dto, remoteRequest, institutionResponse);
            dto.setBillNo(formatBillNo(plan));
            dto.setInfo1((plan != null ? plan.getTahsilatTuru() : "") + "-");
            dto.setBillAmount(plan != null && plan.getTutar() != null ? plan.getTutar() : BigDecimal.ZERO);
            billDTOS.add(dto);
        }
    }

    // 2) Konut Kredisi Ödeme Planları
    OdemePlaniDTOsWrapper konutWrp = institutionResponse.getKonutKredisiOdemePlanIDTOs();
    if (hasPlans(konutWrp)) {
        for (OdemePlaniIDTO plan : plansOf(konutWrp)) {
            BaseBillIDTO dto = new BaseBillIDTO();
            fillCommonFields(dto, remoteRequest, institutionResponse);
            dto.setBillNo(formatBillNo(plan));
            dto.setInfo1((plan != null ? plan.getTahsilatTuru() : "") + "-");
            dto.setBillAmount(plan != null && plan.getTutar() != null ? plan.getTutar() : BigDecimal.ZERO);
            billDTOS.add(dto);
        }
    }

    // 3) EMS üyesi mi? (tek standart fatura)
    if (institutionResponse.isEmsUyesiMi()) {
        billDTOS.add(newEmsBill(remoteRequest, institutionResponse));
    }

    // 4) EMS rezerv ödeme: 1.,2.,3. pay varsa her biri için 1 fatura; flag true ise ekstra 1 fatura
    if (institutionResponse.isEmsUyesiMi() && institutionResponse.getEmsRezervOdemeleriIDTO() != null) {
        EmsRezervOdemeleriIDTO ems = institutionResponse.getEmsRezervOdemeleriIDTO();

        if (ems.getBirinciPayArtirimTutari() != null) {
            billDTOS.add(newEmsBill(remoteRequest, institutionResponse));
        }
        if (ems.getIkinciPayArtirimTutari() != null) {
            billDTOS.add(newEmsBill(remoteRequest, institutionResponse));
        }
        if (ems.getUcuncuPayArtirimTutari() != null) {
            billDTOS.add(newEmsBill(remoteRequest, institutionResponse));
        }
        if (Boolean.TRUE.equals(ems.isRezervArtirimBilgisiVarMi())) {
            billDTOS.add(newEmsBill(remoteRequest, institutionResponse));
        }
    }

    response.setBills(billDTOS);
    return response;
}

/* ===================== yardımcılar (aynı sınıfta) ===================== */

// wrapper null/empty mi?
private boolean hasPlans(OdemePlaniDTOsWrapper wrapper) {
    return wrapper != null
        && wrapper.getOdemePlaniIDTOs() != null
        && !wrapper.getOdemePlaniIDTOs().isEmpty();
}

// güvenli liste ver
private List<OdemePlaniIDTO> plansOf(OdemePlaniDTOsWrapper wrapper) {
    if (wrapper == null) return java.util.Collections.emptyList();
    List<OdemePlaniIDTO> list = wrapper.getOdemePlaniIDTOs();
    return list != null ? list : java.util.Collections.<OdemePlaniIDTO>emptyList();
}

// ortak alanlar
private void fillCommonFields(BaseBillIDTO billDTO, QueryBillsRequest req, OyakResponse inst) {
    billDTO.setProduct(req.getProduct());
    billDTO.setInstitution(req.getInstitution());
    billDTO.setSubscriberNo(req.getMergedSubscriberNumber());
    billDTO.setBillIssueDate(LocalDate.now());
    String ad = inst != null ? inst.getAdi() : null;
    String soyad = inst != null ? inst.getSoyadi() : null;
    billDTO.setSubscriberName((ad != null ? ad : "") + (soyad != null ? soyad : ""));
    billDTO.setInstitutionServiceType(req.getInstitutionServiceType());
    billDTO.setIsDeclarationPayable(false);
    billDTO.setCurrency(req.getCurrencyCode());
}

// EMS için standart fatura
private BaseBillIDTO newEmsBill(QueryBillsRequest req, OyakResponse inst) {
    BaseBillIDTO dto = new BaseBillIDTO();
    fillCommonFields(dto, req, inst);
    dto.setBillNo("standdan alınacak");
    dto.setBillAmount(BigDecimal.ZERO);
    // dto.setInfo1("-");
    return dto;
}

// fatura no formatlama (ihtiyacına göre değiştir)
private String formatBillNo(OdemePlaniIDTO plan) {
    if (plan == null) return "";
    String odeyici = plan.getOdeyiciID();
    String krediId = plan.getKrediId();
    String tahsil = plan.getTahsilatTuru();
    return (odeyici != null ? odeyici : "")
         + (krediId != null ? krediId : "")
         + (tahsil != null ? tahsil : "");
}
