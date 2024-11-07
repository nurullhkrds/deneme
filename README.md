public DoPaymentDetailReconciliationResponse doPaymentDetailReconciliation(DoPaymentDetailReconciliationRequest remoteRequest) {
        DoPaymentDetailReconciliationResponse response = new DoPaymentDetailReconciliationResponse();
        setBaseFields(remoteRequest, response);


        if (remoteRequest.getReconciliationDate() == null) {
            response.setStatus(BillPaymentsConsts.RESPONSE_STATUS.ERROR);
            response.setInstitutionResultCode(EnumResponseCodes.GENERIC__UNKNOWN_ERROR.getValue());
            response.setInstitutionResultDetail(ProcessConsts.INTERNAL_ERROR_CODES.IA__NULL_REQUEST_RECEIVED);
            return response;
        }

        long bankaKodu=1005;
        String parola="Yk79bank";

        int day = remoteRequest.getReconciliationDate().getDayOfMonth();
        int month = remoteRequest.getReconciliationDate().getMonthValue();
        int year = remoteRequest.getReconciliationDate().getYear();
        TahsilatSuRaporDetay mutabakatG = new TahsilatSuRaporDetay();

        mutabakatG.setBankaKodu(bankaKodu);
        mutabakatG.setSifre(parola);

        BeanTahsilatRaporSorgu wsRequest = new BeanTahsilatRaporSorgu();
        wsRequest.setTahsilatBslTarihiGun(day);
        wsRequest.setTahsilatBslTarihiAy(month);
        wsRequest.setTahsilatBslTarihiYil(year);
        wsRequest.setTahsilatBtsTarihiGun(day);
        wsRequest.setTahsilatBtsTarihiAy(month);
        wsRequest.setTahsilatBtsTarihiYil(year);

        mutabakatG.setRapor(wsRequest);
        ArrayOfBeanTahsilatRapor wsResponse = getSuKilisService()
                .tahsilatSuRaporDetay(bankaKodu, parola, wsRequest);

        List<BeanTahsilatRapor> responseList= wsResponse.getBeanTahsilatRapor();
        responseList.forEach(beanTahsilatRapor -> {
           
        });
        
        String responseInternalResultCode = String.valueOf(0);
        resolveResponseMessage(responseInternalResultCode, response);

        if (BillPaymentsConsts.RESPONSE_STATUS.SUCCESS.equals(response.getStatus())) {

        }

        return null;
    }
