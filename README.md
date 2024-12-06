 @Override
    public DoPaymentSummaryReconciliationResponse doPaymentSummaryReconciliation(DoPaymentSummaryReconciliationRequest remoteRequest) {
        DoPaymentSummaryReconciliationResponse response = new DoPaymentSummaryReconciliationResponse();
        setBaseFields(remoteRequest, response);
        Holder<MutabakatBilgisi> mutabakatResult = new Holder<>();

        Holder<Sonuc> sonuc = new Holder<>();
        Mutabakat mutabakat = new Mutabakat();

        XMLGregorianCalendar reconDate = getXmlGregorianCalendar(remoteRequest.getReconciliationDate());

        mutabakat.setTarih(reconDate);
        mutabakat.setIptalOdemeSayisi(new BigDecimal(remoteRequest.getTotalPaymentCancelCount()));
        mutabakat.setIptalOdemeTutari(remoteRequest.getTotalPaymentCancelAmount());
        mutabakat.setToplamOdemeSayisi(new BigDecimal(remoteRequest.getTotalPaymentCount()));
        mutabakat.setToplamOdemeTutari(remoteRequest.getTotalPaymentAmount());
        mutabakat.setGecerliOdemeSayisi(new BigDecimal(remoteRequest.getTotalPaymentCount() - remoteRequest.getTotalPaymentCancelCount()));
        mutabakat.setGecerliOdemeTutari(remoteRequest.getTotalPaymentAmount().subtract(remoteRequest.getTotalPaymentCancelAmount()));

        getSeferihisarService().mutabakat(mutabakat, mutabakatResult, sonuc);

        resolveResponseMessage(sonuc.value.getHataKodu().toString(), response);
        List<String> validResultCodes = Arrays.asList("620", "621", "622", "623", "624", "625");
        if (BillPaymentsConsts.RESPONSE_STATUS.SUCCESS.equals(response.getStatus()) ||
                validResultCodes.contains(response.getInstitutionResultCode())) {

            response.setTotalPaymentAmount(mutabakatResult.value.getToplamOdemeTutari());
            response.setTotalPaymentCount(mutabakatResult.value.getToplamOdemeSayisi().intValue());
            response.setTotalPaymentCancelAmount(mutabakatResult.value.getIptalOdemeTutari());
            response.setTotalPaymentCancelCount(mutabakatResult.value.getIptalOdemeSayisi().intValue());
        }


        setRemoteResponseData(response, remoteRequest,
                PYMLogUtil.convertObjectToJsonString(mutabakat), PYMLogUtil.convertObjectToJsonString(sonuc.value));

        return response;
    }

BUNUN TESTİ AŞAĞIDAKİ GİBİDİR FAKAT BEN   validResultCodes.contains(response.getInstitutionResultCode()) BUNU EKLEDİM BUNUN İÇİNDE YAZMAK GEREKİYOR.


  @Test
    void givenDoPaymentSummaryReconciliationRequest_whenPaymentSummary_thenReturnDoPaymentSummaryReconciliationResponse() {
        DoPaymentSummaryReconciliationRequest remoteRequest = new DoPaymentSummaryReconciliationRequest();
        remoteRequest.setInstitution("SEFERHİSAR");
        remoteRequest.setProduct("BELEDİYE");
        LocalDate reconciliationDate = getLocalDate("2024-10-25");
        BigDecimal amount = new BigDecimal(312.99).setScale(2, RoundingMode.CEILING);
        BigDecimal cancelAmount = new BigDecimal(31.30).setScale(2, RoundingMode.CEILING);
        remoteRequest.setReconciliationDate(reconciliationDate);
        remoteRequest.setTotalPaymentAmount(amount);
        remoteRequest.setTotalPaymentCount(1);
        remoteRequest.setTotalPaymentCancelAmount(cancelAmount);
        remoteRequest.setTotalPaymentCancelCount(1);
        insertReturnMap();
        DoPaymentSummaryReconciliationResponse response = testPaymentSummary(ITahsilatIslemleri.class, remoteRequest);
        assertEquals(2, response.getTotalPaymentCount());
    }


 List<String> validResultCodes = Arrays.asList("620", "621", "622", "623", "624", "625");

  if (BillPaymentsConsts.RESPONSE_STATUS.SUCCESS.equals(response.getStatus()) ||
                validResultCodes.contains(response.getInstitutionResultCode())) {

            response.setTotalPaymentAmount(mutabakatResult.value.getToplamOdemeTutari());
            response.setTotalPaymentCount(mutabakatResult.value.getToplamOdemeSayisi().intValue());
            response.setTotalPaymentCancelAmount(mutabakatResult.value.getIptalOdemeTutari());
            response.setTotalPaymentCancelCount(mutabakatResult.value.getIptalOdemeSayisi().intValue());
        }
