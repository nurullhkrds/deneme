public void onGotoNextPage(EventData events, DisplayContext dc, ConversationContextManager cc) {
    try {
        events.setControllerEvent("StayOnPage");
        IFrmCorporateDefinitionFacade service = null;
        AutoCorporateModelDTO dto = screen2DTO(events, dc, cc);
        String btnName = events.getUserEventWidgetID();
        String widgetName = btnName.replace(BUTTON_PREFIX, "");

        Scope scope = Scope.FW_SESSION_SCOPE;
        if (btnName.equals(DCKEY_BTNPG1111PAYMENTCHANNEL)) { // KRMDET
            scope = Scope.FW_INTER_PROCESS_SCOPE;
            cc.put("toCorporateDetail", "1", Scope.FW_CLIENT_PAGE_SCOPE);
            cc.put("detailPageProcessId", "560", Scope.FW_CLIENT_PAGE_SCOPE);

            // Service kullanarak veri çekme ve session'a atama
            service = JABSSupport.getJABS().getRemote(IFrmCorporateDefinitionFacade.class);
            RequestCorporateDetails request = new RequestCorporateDetails();
            request.setCorporate(dto.getCorporate());
            request.setProduct(dto.getProduct());

            // Servis çağrısını yaparak response alıyoruz
            ResponseCorporateDetails response = service.getCorporateDetails(request);

            // Gelen yanıtı session'a kaydediyoruz
            if (response != null) {
                cc.put("corporateDetails", response, scope);
            }
        } else if (btnName.equals(DCKEY_BTNPG1111PSCDEF)) { // PSCGIR
            scope = Scope.FW_INTER_PROCESS_SCOPE;
            cc.put("toCorporateDetail", "1", Scope.FW_CLIENT_PAGE_SCOPE);
            cc.put("detailPageProcessId", "562", Scope.FW_CLIENT_PAGE_SCOPE);
        } else if (btnName.equals(DCKEY_BTNPG1111LIMIT)) { // ABNLIM kontrolleri
            service = JABSSupport.getJABS().getRemote(IFrmCorporateDefinitionFacade.class);
            RequestIsAutomaticCreditPaymentExist request = new RequestIsAutomaticCreditPaymentExist();
            request.setCorporate(dto.getCorporate());
            request.setProduct(dto.getProduct());
            service.isAutomaticCreditPaymentExist(request);
            scope = Scope.FW_INTER_PROCESS_SCOPE;
            cc.put("toCorporateDetail", "1", Scope.FW_CLIENT_PAGE_SCOPE);
            cc.put("detailPageProcessId", "589", Scope.FW_CLIENT_PAGE_SCOPE);
        } else if (btnName.equals(DCKEY_BTNPG1111SPECIALRATE)) { // DBSKUR kontrolleri
            service = JABSSupport.getJABS().getRemote(IFrmCorporateDefinitionFacade.class);
            RequestHasYpAndSupportAccount request = new RequestHasYpAndSupportAccount();
            request.setCorporate(dto.getCorporate());
            request.setProduct(dto.getProduct());
            service.hasYpAndSupportAccount(request);
            scope = Scope.FW_INTER_PROCESS_SCOPE;
            cc.put("toCorporateDetail", "1", Scope.FW_CLIENT_PAGE_SCOPE);
            cc.put("detailPageProcessId", "588", Scope.FW_CLIENT_PAGE_SCOPE);
        }
        cc.put("URUN", dto.getProduct(), scope);
        cc.put("KURUM", dto.getCorporate(), scope);
        cc.put("KURUMADI", dto.getCorporateName(), scope);
        cc.put("KRMADI", dto.getCorporateName(), scope);
        cc.put("KURUMCIF", dto.getCorporateCif(), scope);
        cc.put("KURUMTIP", dto.getPaymentType(), scope);
        cc.put("MINLIMIT", dto.getMinLimit(), scope);
        cc.put("MAXFAIZ", dto.getMaxInterest(), scope);

        events.setControllerEvent("OpenFirm" + widgetName);
    } catch (Exception e) {
        MessagesUtil.showExceptionMessage(dc, events, e);
        try {
            cc.put("toCorporateDetail", "0", Scope.FW_CLIENT_PAGE_SCOPE);
        } catch (Exception e1) {
            MigrationUtils.sysLog(e1);
        }
    }
}
