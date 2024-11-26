public void display(EventData events, DisplayContext dc, ConversationContextManager cc) {
		try {

			String taskId = cc.getPreviousTaskID();
			if (StringUtils.hasText(taskId)) {
				cc.put("toCorporateDetail", "0", Scope.FW_CLIENT_PAGE_SCOPE);
				return;
			}
			/* H+ kapsamında eskiye uyumluluk için */
			setListviewsDefaultFormats(events, dc, cc);
			/*                                  */
			Session.CURRENT_LISTVIEW.setValue(null, cc);

			List<IKeyValuePair> pairListSubscriberInfoType = new ArrayList<IKeyValuePair>();
			pairListSubscriberInfoType.add(new KeyValuePair("MAX", "MAX"));
			pairListSubscriberInfoType.add(new KeyValuePair("FIX", "FIX"));
			getCmbPG1111SubscriberInfoType1(dc).addElements(pairListSubscriberInfoType);
			getCmbPG1111SubscriberInfoType2(dc).addElements(pairListSubscriberInfoType);
			getCmbPG1111SubscriberInfoType3(dc).addElements(pairListSubscriberInfoType);

			List<IKeyValuePair> pairListPaymentType = new ArrayList<IKeyValuePair>();
			pairListPaymentType.add(new KeyValuePair("S", "Standart"));
			pairListPaymentType.add(new KeyValuePair("D", "DBS"));
			pairListPaymentType.add(new KeyValuePair("A", "Akıllı Kart"));
			pairListPaymentType.add(new KeyValuePair("K", "Kredili Ödeme"));
			getCmbPG1111PaymentType(dc).addElements(pairListPaymentType);

			List<IKeyValuePair> pairListProductType = new ArrayList<IKeyValuePair>();
			pairListProductType.add(new KeyValuePair("B", "Bireysel Ürünler"));
			pairListProductType.add(new KeyValuePair("N", "Nakit Yönetimi"));
			pairListProductType.add(new KeyValuePair("S", "Sigorta"));
			getCmbPG1111ProductType(dc).addElements(pairListProductType);

			List<IKeyValuePair> pairListRateType = new ArrayList<IKeyValuePair>();
			pairListRateType.add(new KeyValuePair("S", "SonOdmTrh ile bul"));
			pairListRateType.add(new KeyValuePair("F", "FaturaTarihi ile bul"));
			getCmbPG1111RateType(dc).addElements(pairListRateType);

			List<IKeyValuePair> pairListScanType = new ArrayList<IKeyValuePair>();
			pairListScanType.add(new KeyValuePair("T", "TakvimGünü Kullan"));
			pairListScanType.add(new KeyValuePair("I", "İşGünü Kullan"));
			getCmbPG1111ScanType(dc).addElements(pairListScanType);

			List<IKeyValuePair> pairListOzolType = new ArrayList<IKeyValuePair>();
			pairListOzolType.add(new KeyValuePair("FTRGENEL", "FTRGENEL"));
			getCmbPG1111OzolType(dc).addElements(pairListOzolType);

			List<IKeyValuePair> pairListCurrencyType = new ArrayList<IKeyValuePair>();
			pairListCurrencyType.add(new KeyValuePair("TCMBA", "TCMB Alış"));
			pairListCurrencyType.add(new KeyValuePair("TCMBS", "TCMB Satış"));
			pairListCurrencyType.add(new KeyValuePair("GİŞEA", "GİŞE Alış"));
			pairListCurrencyType.add(new KeyValuePair("GİŞES", "GİŞE Satış"));
			pairListCurrencyType.add(new KeyValuePair("DUMPA", "Harmoni Alış"));
			pairListCurrencyType.add(new KeyValuePair("DUMPS", "Harmoni Satış"));
			getCmbPG1111CurrencyType(dc).addElements(pairListCurrencyType);
			
			List<IKeyValuePair> pairListExchangeRateType = new ArrayList<IKeyValuePair>();
			pairListExchangeRateType.add(new KeyValuePair("TCMBA", "TCMB Alış"));
			pairListExchangeRateType.add(new KeyValuePair("TCMBS", "TCMB Satış"));


			getCmbpg1111ExchangeRate(dc).addElements(pairListExchangeRateType);

			List<IKeyValuePair> pairListApprovementType = new ArrayList<IKeyValuePair>();
			pairListApprovementType.add(new KeyValuePair("X", "Onaylama Yok"));
			pairListApprovementType.add(new KeyValuePair("O", "Onayla"));
			pairListApprovementType.add(new KeyValuePair("B", "Boşalt+Onayla"));
			pairListApprovementType.add(new KeyValuePair("X", "Onaylama Yok"));
			pairListApprovementType.add(new KeyValuePair("R", "RiskGüncelle+Boşalt+Onayla"));
			getCmbPG1111ApprovementType(dc).addElements(pairListApprovementType);

			List<IKeyValuePair> pairListSubscriberValue = new ArrayList<IKeyValuePair>();
			pairListSubscriberValue.add(new KeyValuePair("0", "0 - AboneNo1"));
			pairListSubscriberValue.add(new KeyValuePair("1", "1 - AboneNo1 + AboneNo2"));
			pairListSubscriberValue.add(new KeyValuePair("2", "2 - AboneNo1 + AboneNo2 + AboneNo3"));
			getCmbPG1111SubscriberValue(dc).addElements(pairListSubscriberValue);

			List<IKeyValuePair> pairListSameDayTerm = new ArrayList<IKeyValuePair>();
			pairListSameDayTerm.add(new KeyValuePair("E", "Gönderebilsin"));
			pairListSameDayTerm.add(new KeyValuePair("H", "Gönderemesin"));
			pairListSameDayTerm.add(new KeyValuePair("X", "Bayi Belirlesin"));
			getCmbPG1111SameDayTerm(dc).addElements(pairListSameDayTerm);

			dc.setHTMLElementRendered(RHAS_COMMISSION_SOURCE_ROW_ID, false);

			ResponseCorporateDefinitionDisplay response = JABSSupport.getJABS().getRemote(IFrmCorporateDefinitionFacade.class).corporateDefinitionDisplay(null);

			String[] ozolList = FWConversionUtil.listToArray(response.getOzolTypeList(), String.class);
			List<IKeyValuePair> pairListOzolList = new ArrayList<IKeyValuePair>();
			for (int i = 0; i < ozolList.length; i++) {
				pairListOzolList.add(new KeyValuePair(ozolList[i], ozolList[i]));
			}
			getCmbPG1111OzolType(dc).addElements(pairListOzolList);

			ResponseProductTree responseProductTree = JABSSupport.getJABS().getRemote(IFrmCorporateDefinitionFacade.class).getProductTree();

			String[] productTreeList = FWConversionUtil.listToArray(responseProductTree.getProductType(), String.class);
			List<IKeyValuePair> pairListProductTreeList = new ArrayList<IKeyValuePair>();
			for (int i = 0; i < productTreeList.length; i++) {
				pairListProductTreeList.add(new KeyValuePair(productTreeList[i], productTreeList[i]));
			}

			getCmbPG1111CorporateProductTree(dc).addElements(pairListProductTreeList);

			loadParamsToCombo(FWConversionUtil.listToArray(response.getSlipTypes(), ParamModelDTO.class), getCmbPG1111SlipType(dc));
			loadParamsToCombo(FWConversionUtil.listToArray(response.getCorporateSlipTypes(), ParamModelDTO.class), getCmbPG1111CorporateSlipType(dc));

			Session.EMPTY_DATA_ADDED.setValue(false, cc);
			Session.SLIP_FIELDS.setValue(FWConversionUtil.listToArray(response.getSlipFields(), ParamModelDTO.class), cc);
			Session.TELLER_SLIP_FIELDS.setValue(FWConversionUtil.listToArray(response.getVznFields(), ParamModelDTO.class), cc);

			// getFirstRecord(events,dc,cc);
			toInquiryMode(events, dc, cc);
			lockScreen(false, events, dc, cc);
			ComponentUtils.disableButton(getBtnPG1111Confirm(dc));

			if (isScreenReadOnly(events, dc, cc)) {
				toApprovementMode(events, dc, cc);
			}
		} catch (Exception e) {
			MessagesUtil.showExceptionMessage(dc, events, e);
		}

		MigrationUtils.hideStepper(cc);
	}


BU ÖRNEK BİR ŞEY BURADA SESSİONA BURDA VERİYİ SETLEMİŞ 



    public void display(EventData events, DisplayContext dc, ConversationContextManager cc) {

        try {
            MigrationUtils.hideStepper(cc);

            //defaulst session values
            setDefaultSessionValues(cc);

            fillAllStaticCombos(events, dc, cc);
            if (Constants.SUPPLIER.toString().equals(Session.PRODUCT.getSessionValue(cc, String.class))) {
                getLblMICreditPaymentPG1112(dc).setValue("ISKN Hesap Tanımlanabilsin");//ISKN_ALLOWED
            }

            RequestCorporateDetailDisplay request = new RequestCorporateDetailDisplay();
            request.setCorporate(Session.CORPORATE.getSessionValue(cc, String.class));
            request.setProduct(Session.PRODUCT.getSessionValue(cc, String.class));
            request.setCorporateType(Session.CORPORATE_TYPE.getSessionValue(cc, String.class));

            ResponseCorporateDetailDisplay response;
            response = JABSSupport.getJABS().getRemote(IFrmCorporateDetailFacade.class).display(request);

            Session.PYMTYPE_COMBO_LIST.setValue(response.getPaymentList(), cc);
            //Onay kontrolü
            if (NotificationParametersParser.isTypeApprovalOrScreenReadOnly(cc) || NotificationParametersParser.isTypeBackAfterApproval(cc)) {
                setCorporateInfo(Session.PRODUCT.getSessionValue(cc, String.class), Session.CORPORATE.getSessionValue(cc, String.class), dc, cc, events);
                toApprovementMode(events, dc, cc);
                return;
            }
            setCorporateInfo(Session.PRODUCT.getSessionValue(cc, String.class), Session.CORPORATE.getSessionValue(cc, String.class), dc, cc, events);
            setHeaderText(null, null, events, dc, cc);
            Session.CORPORATE_DETAIL_LIST.setValue(response.getAutoCorporateDetailDTOList(), cc);
            fillScreen(events, dc, cc, FWConversionUtil.listToArray(response.getAutoCorporateDetailDTOList(), AutoCorporateDetailDTO.class), false);
            showDetailValidateMessages(response.getErrMap(), events, dc, cc);

        } catch (Exception e) {
            MessagesUtil.showExceptionMessage(dc, events, e);
        }

    }
