	public void ongotoBillPayments2(EventData events, DisplayContext dc, ConversationContextManager cc) throws FWAbendException {
		try {
			String isAnyInputChangedFlag = null;

			if (cc.existsInConversationScope(CONTEXT_KEY_IsAnyInputChangedFlag)) {
				isAnyInputChangedFlag = (String) cc.getFromConversationScope(CONTEXT_KEY_IsAnyInputChangedFlag);
			}

			if (NotificationParametersParser2.isTypeApproval(cc) || NotificationParametersParser2.isTypeBackAfterApproval(cc)
					|| NotificationParametersParser.isTypeApprovalOrScreenReadOnly(cc)) {
				if (isAnyInputChangedFlag == null) {
					events.setControllerEvent(CEKEY_GOTOBILLPAYMENTS2_TRUE);
					cc.putToConversationScope("IS_FROM_STEP1", Boolean.TRUE);
					return;
				}
				callClearParameters(cc, "3455");

				if (cc.existsInConversationScope(CONTEXT_KEY_ApprovalFlag)) {
					cc.removeFromConversationScope(CONTEXT_KEY_ApprovalFlag);
				}
			}

			if (getDialogMessageFlag(cc) != null && getDialogMessageFlag(cc).equalsIgnoreCase("1")) {
				cc.putToPageScope(CONTEXT_KEY_DialogMessageFlag, "");

				events.setControllerEvent(CEKEY_GOTOBILLPAYMENTS2_TRUE);

				return;
			}

			events.setControllerEvent(CEKEY_GOTOBILLPAYMENTS2_CANCEL);

			String memberNo = "", info1 = "", info2 = "", info9 = "";

			BillsDTO[] billsDTO = (BillsDTO[]) cc.getFromPageScope(CONTEXT_KEY_BillsDTO);
			BillSelectDTO billSelectDTO = (BillSelectDTO) cc.getFromPageScope(CONTEXT_KEY_BillSelectDTO);
			ParamsDTO paramsDTO = (ParamsDTO) cc.getFromPageScope(CONTEXT_KEY_ParamsDTO);
			ScreenDTO screenDTO = (ScreenDTO) cc.getFromPageScope(CONTEXT_KEY_ScreenDTO);
			ChkAboneDTO chkAboneDTO = (ChkAboneDTO) cc.getFromPageScope(CONTEXT_KEY_ChkAboneDTO);
			ChkAboneExtraDTO chkAboneExtraDTO = (ChkAboneExtraDTO) cc.getFromPageScope(CONTEXT_KEY_ChkAboneExtraDTO);

			ItemsDTO[] itemsDTO = null;

			String branchCode = (String) cc.getFromPageScope(CONTEXT_KEY_BranchCode);

			if (getPG118_BillPaymentsTedasCorporationPaymentDynamicRegion(dc).isVisible()) {
				memberNo = (String) cc.getFromPageScope(CONTEXT_KEY_MemberNo);
			}
			if (getPG136_BillPaymentsCorporationPaymentDynamicRegion(dc).isVisible()) {
				itemsDTO = (ItemsDTO[]) cc.getFromPageScope(CONTEXT_KEY_ItemsDTO);
			}

			// Needed DTO parameters passed to the conversation scope
			if (cc.existsInConversationScope(CONTEXT_KEY_ChkAboneDTO))
				cc.removeFromConversationScope(CONTEXT_KEY_ChkAboneDTO);
			if (cc.existsInConversationScope(CONTEXT_KEY_ChkAboneExtraDTO))
				cc.removeFromConversationScope(CONTEXT_KEY_ChkAboneExtraDTO);
			if (cc.existsInConversationScope(CONTEXT_KEY_BillSelectDTO))
				cc.removeFromConversationScope(CONTEXT_KEY_BillSelectDTO);
			if (cc.existsInConversationScope(CONTEXT_KEY_ParamsDTO))
				cc.removeFromConversationScope(CONTEXT_KEY_ParamsDTO);
			if (cc.existsInConversationScope(CONTEXT_KEY_ScreenDTO))
				cc.removeFromConversationScope(CONTEXT_KEY_ScreenDTO);
			if (cc.existsInConversationScope(CONTEXT_KEY_ExchangeRate))
				cc.removeFromConversationScope(CONTEXT_KEY_ExchangeRate);

			cc.putToConversationScope(CONTEXT_KEY_ParamsDTO, paramsDTO);

			if (screenDTO == null)
				screenDTO = new ScreenDTO();
			if (billsDTO != null)
				cc.putToConversationScope(CONTEXT_KEY_BillsDTO, billsDTO);
			if (branchCode != null)
				cc.putToConversationScope(CONTEXT_KEY_BranchCode, branchCode);
			if (billSelectDTO != null)
				cc.putToConversationScope(CONTEXT_KEY_BillSelectDTO, billSelectDTO);
			if (chkAboneDTO != null)
				cc.putToConversationScope(CONTEXT_KEY_ChkAboneDTO, chkAboneDTO);
			if (chkAboneExtraDTO != null)
				cc.putToConversationScope(CONTEXT_KEY_ChkAboneExtraDTO, chkAboneExtraDTO);
			if (itemsDTO != null)
				cc.putToConversationScope(CONTEXT_KEY_ItemsDTO, itemsDTO);

			// Y and N stands for if the 3rd party account has more than one bill or not
			if (billsDTO != null && billsDTO.length > 1)
				screenDTO.setCityName(getCmb_P136_PI_City(dc).getSelectedKey() + ";Y");
			else
				screenDTO.setCityName(getCmb_P136_PI_City(dc).getSelectedKey() + ";N");

			screenDTO.setProdCode(getCmb_P136_PI_Product(dc).getSelectedKey());
			screenDTO.setSrvcType(getCmb_P136_PI_ServiceType(dc).getSelectedKey());
			screenDTO.setFirmCode(getCmb_P136_PI_Enterprise(dc).getSelectedKey());

			int k = 1, s = 0;

			if (getPG136_BillPaymentsCorporationPaymentDynamicRegion(dc).isVisible()) {
				if (checkScreenInputParameters(events, dc, cc)) {
					for (int i = 0; i < itemsDTO.length; i++) {
						if (itemsDTO[i].getItemType().equalsIgnoreCase("TEXT")) {
							Object o = getDynamicGrid(dc).getCell(s, k);
							if (o != null) {
								String value = null;
								if (o instanceof ITextBox) {
									value = ((ITextBox) o).getValue();
								} else if (o instanceof IComboBox) {
									value = ((IComboBox) o).getValue();
								}

								String name = itemsDTO[i].getItemName();
								if (name.equalsIgnoreCase("BILGI1"))
									info1 = value;
								else if (name.equalsIgnoreCase("BILGI2"))
									info2 = value;
								else if (name.equalsIgnoreCase("BILGI9"))
									info9 = value;
							}
						}
						s++;
					}
				} else {
					if (billsDTO == null) {
						PymCommonUtil.addError(events, getProperty("S118_ERROR_MESSAGE_PAYMENT_CONTROL", cc));
						return;
					} else {
						PymCommonUtil.addError(events, getProperty("S136_ERROR_MESSAGE_INPUT_KONTROL_X1", cc));
						return;
					}
				}
			}

			if (billSelectDTO != null) {
				/*
				 * Fill screenDTO with enable fields data from grid
				 */
				ScreenDTO tempScreenDTO = screenDTO;

				screenDTO = fillScreenDTOWithEnableFields(events, dc, cc, screenDTO, itemsDTO);

				if (screenDTO == null) {
					screenDTO = tempScreenDTO;
				}

				if ("DBS".equals(paramsDTO.getProdCode()) && ("GEDİZ".equals(paramsDTO.getFirmCode()) || "AYDEM".equals(paramsDTO.getFirmCode()))) {
					screenDTO.setNarrative(paramsDTO.getFirmCode() + " " + chkAboneDTO.getRealAbone() + " " + billSelectDTO.getBillNo());
				}

				String currencyValue = billSelectDTO.getCurrency();

				if (!checkAmountBeforeNextPage(events, dc, cc)) {
					PymCommonUtil.addError(events, getProperty("S030_ERROR_MESSAGE_MIN_VALUE_CONTROL", cc));
					return;
				}

				BigDecimal paymentAmount = null;

				BigDecimal originalPaymentAmount = null;

				if (getPG136_BillPaymentsCorporationPaymentDynamicRegion(dc).isVisible()) {
					if (getTxt_P136_BP_AmountToBePaid(dc).isVisible())
						paymentAmount = getTxt_P136_BP_AmountToBePaid(dc).getValue();
					else if (billSelectDTO.getNewAmount() != null)
						paymentAmount = billSelectDTO.getNewAmount();
					else
						paymentAmount = billSelectDTO.getBillAmount();
					
					if("DBS".equals(paramsDTO.getProdCode())){
						if (cc.existsInPageScope(CONTEXT_KEY_ExchangeRate)) {
							BigDecimal exhangeRate = (BigDecimal) cc.getFromPageScope(CONTEXT_KEY_ExchangeRate);
							cc.putToConversationScope(CONTEXT_KEY_ExchangeRate, exhangeRate);
						}

						if (cc.existsInPageScope(CONTEXT_KEY_OriginalNewAmount)) {
							originalPaymentAmount = (BigDecimal) cc.getFromPageScope(CONTEXT_KEY_OriginalNewAmount);
						}
					}
				} else if (getPG118_BillPaymentsTedasCorporationPaymentDynamicRegion(dc).isVisible()) {
					String aboneNo = getTxt_P118_Static_BusinessCode1(dc).getValue() + "." + getTxt_P118_Static_BusinessCode2(dc).getValue() + "."
							+ getTxt_P118_Static_BusinessCode3(dc).getValue() + "." + getTxt_P118_Static_BusinessCode4(dc).getValue() + "."
							+ getTxt_P118_Static_BusinessCode5(dc).getValue();
					String aboneNo2 = getTxt_P118_Static_MemberNo(dc).getValue().toString();
					screenDTO.setAboneNo1(aboneNo);
					screenDTO.setAboneNo2(aboneNo2);
					screenDTO.setInfo1(getTxt_P118_Static_Address(dc).getValue());
					screenDTO.setSurname(getTxt_P118_Static_NameSurname(dc).getValue());
					paymentAmount = getTxt_P118_BP_AmountToBePaid(dc).getValue();

				} else if (getPG029_BillPaymentsErdemirCorporationPaymentDynamicRegion(dc).isVisible()) {
					if (cc.existsInPageScope(CONTEXT_KEY_ExchangeRate)) {
						BigDecimal exhangeRate = (BigDecimal) cc.getFromPageScope(CONTEXT_KEY_ExchangeRate);

						cc.putToConversationScope(CONTEXT_KEY_ExchangeRate, exhangeRate);
					}

					if (cc.existsInPageScope(CONTEXT_KEY_OriginalNewAmount)) {
						originalPaymentAmount = (BigDecimal) cc.getFromPageScope(CONTEXT_KEY_OriginalNewAmount);
					}

					paymentAmount = getTxt_P029_BP_AmountToBePaid(dc).getValue();

					screenDTO.setAmount(paymentAmount);

					currencyValue = (String) getTxt_P029_BP_AmountToBePaidFx(dc).getValue();

				} else if (getPG092_BillPaymentsOnlineCorporationPaymentDynamicRegion(dc).isVisible()) {
					paymentAmount = getTxt_P092_BP_AmountToBePaid(dc).getValue();

				} else if (getPG121_BillPaymentsCellPhonePaymentDynamicRegion(dc).isVisible()) {
					screenDTO.setAboneNo1(getTxt_P121_Static_CellNumber2(dc).getValue());
					paymentAmount = getTxt_P121_BP_AmountToBePaid(dc).getValue();
					currencyValue = getTxt_P121_BP_AmountToBePaidFx(dc).getValue();

				} else if (getPG129_BillPaymentsOnlineUniversityPaymentDynamicRegion(dc).isVisible()) {
					screenDTO.setSurname(getTxt_P129_Static_NameSurname(dc).getValue());
					screenDTO.setAboneNo1(getTxt_P129_Static_StudentNoTCKN(dc).getValue());
					screenDTO.setInfo1(getCmb_P129_Static_FXType(dc).getValue());
					paymentAmount = getTxt_P129_BP_AmountToBePaid(dc).getValue();
					currencyValue = getTxt_P129_BP_AmountToBePaidFx(dc).getValue();

				} else if (getPG061_BillPaymentsKocFinanceCorporationDynamicRegion(dc).isVisible()) {
					paymentAmount = getTxt_P061_Static_AmountToBePaid(dc).getValue();
					currencyValue = getTxt_P061_Static_DebitAmountFx(dc).getValue();
					screenDTO.setInfo3(getTxt_P061_Static_NextInstallmentDate(dc).getValue());
					screenDTO.setInfo4(getCr_P061_Static_NextInstallmentAmount(dc).getValue().toString());

				} else if (getPG062_BillPaymentKocFiatCorporationDynamicRegion(dc).isVisible()) {
					if (!screenDTO.getAboneNo1().equals(getTxtPG062BuyerOrBranchCode(dc).getValue())) {
						PymCommonUtil.addError(events, getProperty("S136_ERROR_MESSAGE_INPUT_KONTROL_X1", cc));
						return;
					}

					paymentAmount = getCrbPG062ToPaidAmount(dc).getValue();
					currencyValue = getTxtPG062DebitAmountFx(dc).getValue();
					screenDTO.setInfo3(getTxtPG062NextInstallmentDate(dc).getValue());
					screenDTO.setInfo4(getCrbNextInstallmentAmount(dc).getValue().toString());
				}

				RequestChkOdeme checkOdemeRequest = new RequestChkOdeme();

				if (getPG118_BillPaymentsTedasCorporationPaymentDynamicRegion(dc).isVisible()) {
					checkOdemeRequest.setRealAbone(memberNo);
				} else if (paramsDTO.getProdCode().equalsIgnoreCase("VIZE001")) {
					checkOdemeRequest.setRealAbone(paramsDTO.getBillSeqNo());
				} else if (getPG129_BillPaymentsOnlineUniversityPaymentDynamicRegion(dc).isVisible()) {
					checkOdemeRequest.setRealAbone(getTxt_P129_Static_StudentNoTCKN(dc).getValue());
				} else if (getPG061_BillPaymentsKocFinanceCorporationDynamicRegion(dc).isVisible()) {
					checkOdemeRequest.setRealAbone(getTxt_P061_Static_BuyerOrBranchCode(dc).getValue());
				} else if (getPG062_BillPaymentKocFiatCorporationDynamicRegion(dc).isVisible()) {
					checkOdemeRequest.setRealAbone(getTxtPG062BuyerOrBranchCode(dc).getValue());
				} else if (getPG029_BillPaymentsErdemirCorporationPaymentDynamicRegion(dc).isVisible()) {
					screenDTO.setAboneNo1(getTxt_P029_BP_BranchCode(dc).getValue());
					checkOdemeRequest.setRealAbone(getTxt_P029_BP_BranchCode(dc).getValue());
				} else {
					checkOdemeRequest.setRealAbone(chkAboneDTO.getRealAbone());
				}

				screenDTO.setRealAbone(checkOdemeRequest.getRealAbone());

				checkOdemeRequest.setBranch(branchCode);
				checkOdemeRequest.setFirmCode(paramsDTO.getFirmCode());
				checkOdemeRequest.setProdCode(paramsDTO.getProdCode());
				checkOdemeRequest.setPayType(paramsDTO.getPayType());
				checkOdemeRequest.setBillStatus(billSelectDTO.getStatus());
				checkOdemeRequest.setFactorFirm(billSelectDTO.getFactorFirm());
				checkOdemeRequest.setCurrency(billSelectDTO.getCurrency());
				checkOdemeRequest.setBillAmount(billSelectDTO.getNewAmount());
				checkOdemeRequest.setAmount(paymentAmount);

				if (getPG136_BillPaymentsCorporationPaymentDynamicRegion(dc).isVisible()) {
					checkOdemeRequest.setBilgi1(info1);
					checkOdemeRequest.setBilgi2(info2);
					checkOdemeRequest.setBilgi9(info9);
				}

				try {
					if (originalPaymentAmount != null) {
						checkOdemeRequest.setAmount(originalPaymentAmount);
					}

					ServiceUtil.getBillPaymentsService().checkOdeme(checkOdemeRequest);
				} catch (HmnServiceException e1) {
					if ("WARNING".equals(e1.type)) {
						events.addWarning(e1.text, null, "onWarningApproved", null);
					} else {
						PymCommonUtil.HandleException(events, dc, e1);
					}
				}

				cc.putToConversationScope(CONTEXT_KEY_Amount, paymentAmount);
				cc.putToConversationScope(CONTEXT_KEY_Currency, currencyValue.equalsIgnoreCase(CURRENCY_YTL) ? CURRENCY_TL : currencyValue);
				cc.putToConversationScope(CONTEXT_KEY_ScreenDTO, screenDTO);

				boolean isValid = true;
				if (getPG121_BillPaymentsCellPhonePaymentDynamicRegion(dc).isVisible()) {
					String currentNo = getTxt_P121_Static_CellNumber2(dc).getValue();

					if (!checkOdemeRequest.getRealAbone().equalsIgnoreCase(currentNo)) {
						PymCommonUtil.addError(events, getProperty("S121_ERROR_MESSAGE_PAYMENT_CURRENCY_TEL_NO_CONTROL", cc));
						isValid = false;
					}
				}

				if (isValid) {
					events.setControllerEvent(CEKEY_GOTOBILLPAYMENTS2_TRUE);
					cc.putToConversationScope("IS_FROM_STEP1", Boolean.TRUE);
				} else {
					events.setControllerEvent(CEKEY_GOTOBILLPAYMENTS2_CANCEL);
				}

			} else if (getPG061_BillPaymentsKocFinanceCorporationDynamicRegion(dc).isVisible()) {
				// check amount to be paid
				if (getTxt_P061_Static_AmountToBePaid(dc).getValue() == null || getTxt_P061_Static_AmountToBePaid(dc).getValue().compareTo(new BigDecimal(0)) != 1) {
					PymCommonUtil.addError(events, getProperty("S129_ERROR_MESSAGE_PAYMENT_PROCESS_CONTROL_2", cc));
					events.setControllerEvent(CEKEY_GOTOBILLPAYMENTS2_CANCEL);
					return;
				}

				RequestChkOdeme reqChkOdeme = new RequestChkOdeme();

				reqChkOdeme.setAmount(getTxt_P061_Static_DebitAmount(dc).getValue());
				reqChkOdeme.setCurrency(getTxt_P061_Static_DebitAmountFx(dc).getValue());
				reqChkOdeme.setBranch(branchCode);
				reqChkOdeme.setFirmCode(paramsDTO.getFirmCode());
				reqChkOdeme.setProdCode(paramsDTO.getProdCode());
				reqChkOdeme.setPayType(paramsDTO.getPayType());
				reqChkOdeme.setRealAbone(getTxt_P061_Static_BuyerOrBranchCode(dc).getValue());

				if (billsDTO != null) {
					reqChkOdeme.setBilgi1(billsDTO[0].getInfo1());
					reqChkOdeme.setBilgi2(billsDTO[0].getInfo2());
					reqChkOdeme.setBilgi9(billsDTO[0].getInfo9());
					reqChkOdeme.setBillStatus(billsDTO[0].getStatus());
					reqChkOdeme.setFactorFirm(billsDTO[0].getFactorFirm());
				} else {
					return;
				}

				try {
					ServiceUtil.getBillPaymentsService().checkOdeme(reqChkOdeme);

					screenDTO.setRealAbone(getTxt_P061_Static_BuyerOrBranchCode(dc).getValue());

					cc.putToConversationScope(CONTEXT_KEY_RequestChkOdeme, reqChkOdeme);
					cc.putToConversationScope(CONTEXT_KEY_Amount, getTxt_P061_Static_AmountToBePaid(dc).getValue());
					cc.putToConversationScope(CONTEXT_KEY_Currency, getTxt_P061_Static_DebitAmountFx(dc).getValue());
					cc.putToConversationScope(CONTEXT_KEY_ScreenDTO, screenDTO);

					events.setControllerEvent(CEKEY_GOTOBILLPAYMENTS2_TRUE);

					cc.putToConversationScope("IS_FROM_STEP1", Boolean.TRUE);
				} catch (HmnServiceException e) {
					PymCommonUtil.addError(events, e.getMessage());

					events.setControllerEvent(CEKEY_GOTOBILLPAYMENTS2_CANCEL);
				}

			} else if (getPG062_BillPaymentKocFiatCorporationDynamicRegion(dc).isVisible()) {
				// check amount to be paid
				if (getCrbPG062ToPaidAmount(dc).getValue() == null || getCrbPG062ToPaidAmount(dc).getValue().compareTo(new BigDecimal(0)) != 1) {
					PymCommonUtil.addError(events, getProperty("S129_ERROR_MESSAGE_PAYMENT_PROCESS_CONTROL_2", cc));
					events.setControllerEvent(CEKEY_GOTOBILLPAYMENTS2_CANCEL);
					return;
				}

				RequestChkOdeme reqChkOdeme = new RequestChkOdeme();

				reqChkOdeme.setAmount(getCrbPG062DebitAmount(dc).getValue());
				reqChkOdeme.setCurrency(getTxtPG062DebitAmountFx(dc).getValue());
				reqChkOdeme.setBranch(branchCode);
				reqChkOdeme.setFirmCode(paramsDTO.getFirmCode());
				reqChkOdeme.setProdCode(paramsDTO.getProdCode());
				reqChkOdeme.setPayType(paramsDTO.getPayType());
				reqChkOdeme.setRealAbone(getTxtPG062BuyerOrBranchCode(dc).getValue());

				if (billsDTO != null) {
					reqChkOdeme.setBilgi1(billsDTO[0].getInfo1());
					reqChkOdeme.setBilgi2(billsDTO[0].getInfo2());
					reqChkOdeme.setBilgi9(billsDTO[0].getInfo9());
					reqChkOdeme.setBillStatus(billsDTO[0].getStatus());
					reqChkOdeme.setFactorFirm(billsDTO[0].getFactorFirm());
				} else {
					return;
				}

				try {
					ServiceUtil.getBillPaymentsService().checkOdeme(reqChkOdeme);

					screenDTO.setRealAbone(getTxtPG062BuyerOrBranchCode(dc).getValue());

					cc.putToConversationScope(CONTEXT_KEY_RequestChkOdeme, reqChkOdeme);
					cc.putToConversationScope(CONTEXT_KEY_Amount, getTxt_P061_Static_AmountToBePaid(dc).getValue());
					cc.putToConversationScope(CONTEXT_KEY_Currency, getTxt_P061_Static_DebitAmountFx(dc).getValue());
					cc.putToConversationScope(CONTEXT_KEY_ScreenDTO, screenDTO);

					events.setControllerEvent(CEKEY_GOTOBILLPAYMENTS2_TRUE);

					cc.putToConversationScope("IS_FROM_STEP1", Boolean.TRUE);
				} catch (HmnServiceException e) {
					PymCommonUtil.addError(events, e.getMessage());

					events.setControllerEvent(CEKEY_GOTOBILLPAYMENTS2_CANCEL);
				}

			} else if (getPG030_BillPaymentsErdemirFreeDynamicRegion(dc).isVisible()) {
				chkAboneExtraDTO = null;

				billSelectDTO = new BillSelectDTO();

				if (!checkRequiredFieldsForErdemirFree(events, dc, cc)) {
					return;
				}

				if (paramsDTO != null) {
					RequestChkAboneExtra reqChkAboneExtra = new RequestChkAboneExtra();

					reqChkAboneExtra.setRealAbone(getTxt_P030_Static_CustomerNumber(dc).getValue());
					reqChkAboneExtra.setFirmCode(paramsDTO.getFirmCode());
					reqChkAboneExtra.setProdCode(paramsDTO.getProdCode());
					reqChkAboneExtra.setAdSoyad(getTxt_P030_Static_Address(dc).getValue());

					SimpleDateFormat formatter = new SimpleDateFormat(DateUtil.DATEFORMAT_ddMMyyyy_DASHED);
					GregorianCalendar cal = (GregorianCalendar) Calendar.getInstance();
					String formattedDate = formatter.format(cal.getTime());
					String[] formattedDateArray = formattedDate.split("-");

					billSelectDTO.setBillAmount(getTxt_P030_Static_TransactionAmount(dc).getValue());
					billSelectDTO.setNewAmount(getTxt_P030_Static_TransactionAmount(dc).getValue());
					billSelectDTO.setBillNoDisp(paramsDTO.getBillSeqNo());
					billSelectDTO.setCurrency(getTxt_P030_Static_TransactionAmountFx(dc).getValue());
					billSelectDTO.setSurname(getTxt_P030_Static_Address(dc).getValue());
					billSelectDTO.setExpiry(formattedDateArray[0] + "/" + formattedDateArray[1] + "/" + formattedDateArray[2]);

					try {
						ResponseChkAboneExtra respChkAboneExtra = ServiceUtil.getBillPaymentsService().checkAboneExtra(reqChkAboneExtra);

						chkAboneExtraDTO = respChkAboneExtra.getChkAboneExtra();

						if (chkAboneExtraDTO != null)
							chkAboneExtraDTO.setNarrative("Erdemir Fatura Odemesi : " + chkAboneExtraDTO.getNarrative());

						if (chkAboneExtraDTO.getNarrative() != null)
							screenDTO.setNarrative(chkAboneExtraDTO.getNarrative());

						screenDTO.setAboneNo1(getTxt_P030_Static_CustomerNumber(dc).getValue());
						screenDTO.setRealAbone(reqChkAboneExtra.getRealAbone());

						events.setControllerEvent(CEKEY_GOTOBILLPAYMENTS2_TRUE);

						cc.putToConversationScope("IS_FROM_STEP1", Boolean.TRUE);
						cc.putToConversationScope(CONTEXT_KEY_ChkAboneExtraDTO, chkAboneExtraDTO);
						cc.putToConversationScope(CONTEXT_KEY_Amount, getTxt_P030_Static_TransactionAmount(dc).getValue());
						cc.putToConversationScope(CONTEXT_KEY_Currency, getTxt_P030_Static_TransactionAmountFx(dc).getValue());
						cc.putToConversationScope(CONTEXT_KEY_ScreenDTO, screenDTO);
						cc.putToConversationScope(CONTEXT_KEY_BillSelectDTO, billSelectDTO);

					} catch (HmnServiceException e) {
						PymCommonUtil.addError(events, e.getMessage());

						events.setControllerEvent(CEKEY_GOTOBILLPAYMENTS2_CANCEL);
					}

				}

			} else if (getPG008_BillPaymentsAtilimDynamicRegion(dc).isVisible()) {
				chkAboneExtraDTO = (ChkAboneExtraDTO) cc.getFromConversationScope(CONTEXT_KEY_ChkAboneExtraDTO);

				billSelectDTO = new BillSelectDTO();

				if (chkAboneExtraDTO != null) {
					// TODO : Add log and fix here.
				}

				if (!checkRequiredFieldsForAtilim(events, dc, cc)) {
					return;
				}

				if (chkAboneExtraDTO != null) {
					if (chkAboneExtraDTO.getNarrative() != null)
						screenDTO.setNarrative(chkAboneExtraDTO.getNarrative());

					if (getTxt_P008_Static_TransactionAmount(dc).getValue() == null) {
						events.setControllerEvent(CEKEY_GOTOBILLPAYMENTS2_CANCEL);

						PymCommonUtil.addError(events, getProperty("S030_ERROR_MESSAGE_FILL_REQUIRED_FIELDS", cc));
					}

					events.setControllerEvent(CEKEY_GOTOBILLPAYMENTS2_TRUE);
					cc.putToConversationScope("IS_FROM_STEP1", Boolean.TRUE);
					screenDTO.setRealAbone(chkAboneDTO.getRealAbone());

					SimpleDateFormat formatter = new SimpleDateFormat(DateUtil.DATEFORMAT_ddMMyyyy_DASHED);
					GregorianCalendar cal = (GregorianCalendar) Calendar.getInstance();
					String formattedDate = formatter.format(cal.getTime());
					String[] formattedDateArray = formattedDate.split("-");

					// billSelect
					billSelectDTO.setBillAmount(getTxt_P008_Static_TransactionAmount(dc).getValue());
					billSelectDTO.setNewAmount(getTxt_P008_Static_TransactionAmount(dc).getValue());
					billSelectDTO.setBillNoDisp(paramsDTO.getBillSeqNo());
					billSelectDTO.setCurrency(getTxt_P008_Static_TransactionAmountFx(dc).getValue());
					billSelectDTO.setSurname(getTxt_P008_Static_Title(dc).getValue());
					billSelectDTO.setExpiry(formattedDateArray[0] + "/" + formattedDateArray[1] + "/" + formattedDateArray[2]);

					// screenDTO
					screenDTO.setAboneNo1(getTxt_P008_Static_BranchCode(dc).getValue());
					screenDTO.setAmount(getTxt_P008_Static_TransactionAmount(dc).getValue());

					cc.putToConversationScope(CONTEXT_KEY_ChkAboneExtraDTO, chkAboneExtraDTO);
					cc.putToConversationScope(CONTEXT_KEY_Amount, getTxt_P008_Static_TransactionAmount(dc).getValue());
					cc.putToConversationScope(CONTEXT_KEY_Currency, getTxt_P008_Static_TransactionAmountFx(dc).getValue());
					cc.putToConversationScope(CONTEXT_KEY_ScreenDTO, screenDTO);
					cc.putToConversationScope(CONTEXT_KEY_BillSelectDTO, billSelectDTO);

				} else {
					// inquiry criteria changed
					if (getTxt_P008_Static_BranchCode(dc).getValue() != null && getTxt_P008_Static_Title(dc).getValue() != null
							&& !getTxt_P008_Static_Title(dc).getValue().equals("")) {
						getTxt_P008_Static_TransactionAmount(dc).clear();
						getTxt_P008_Static_BranchCode(dc).clear();
						getTxt_P008_Static_Title(dc).clear();
						getTxt_P008_Static_Address(dc).clear();

						events.setControllerEvent(CEKEY_GOTOBILLPAYMENTS2_CANCEL);

						PymCommonUtil.addError(events, getProperty("S030_ERROR_MESSAGE_INQUIRY_CRITER_CHANGED", cc));
					} else {
						events.setControllerEvent(CEKEY_GOTOBILLPAYMENTS2_CANCEL);

						PymCommonUtil.addError(events, getProperty("S030_ERROR_MESSAGE_FILL_REQUIRED_FIELDS", cc));
					}
				}

			} else {
				if (billsDTO == null) {
					PymCommonUtil.addError(events, getProperty("S118_ERROR_MESSAGE_PAYMENT_CONTROL", cc));
				} else {
					PymCommonUtil.addError(events, getProperty("S136_ERROR_MESSAGE_INPUT_KONTROL_X2", cc));
				}

				events.setControllerEvent(CEKEY_GOTOBILLPAYMENTS2_CANCEL);

			}

			BigDecimal paymentAmount = (BigDecimal) cc.getFromConversationScope(CONTEXT_KEY_Amount);

			Map<String, String> callScriptParameters = new HashMap<String, String>();
			callScriptParameters.put("CorporationName", paramsDTO.getFirmName());
			callScriptParameters.put("SubscriberNumber", screenDTO.getRealAbone());
			if (billSelectDTO != null) {
				callScriptParameters.put("ExpiryDate", billSelectDTO.getExpiry());
				if (paymentAmount != null) {
					callScriptParameters.put("Amount", paymentAmount.toString());
					cc.putToConversationScope(CONTEXT_KEY_CallScriptParameters, callScriptParameters);
				}
			}

		} catch (Exception e) {
			throw new FWAbendException(FWPrivateConstants.AT_ABEND_ERRORID, null, null, e);
		}
	}




BEN BURDA BU FONSKYİNOU TETİKLEDİĞİMDE "BU KURUM İÇİN YTL TANIMLI DEĞİL" HATASI ALIYORUM
