@Override
	public void sendCFPEvent(BillPaymentCFPEventDTO eventDTO) throws HmnServiceException {

		if (Arrays.asList("SİGORTA", "SPT").contains(eventDTO.getProduct())) {
			CfpBillPaymentEventDTO req = createCfpBillPaymentEventDTO(eventDTO);
			ServiceLocator.getIInsCfpEventPublisher().publishBillPaymentEvent(req);
			return;
		}

		if (!eligibleProductList.contains(eventDTO.getProduct())) {
			return;
		}

		IPymCFPEventPreparer pymCFPEventPreparer = PymEventPreparerFactory.getPymCFPEventPreparer(eventDTO.getEventType());
		CfpEventPrepareResultDTO preparedEventData = pymCFPEventPreparer.prepareEventData(eventDTO);

		for (CfpProductEventDTOResult productEvenResulttDTO : preparedEventData.getResults()) {
			if (EnumCfpEventEnrichmentResult.SUCCESS.equals(productEvenResulttDTO.getResult())) {

				BillCFPSendEventDataResult sendResult = sendEventData(productEvenResulttDTO.getProductEventDTO());
				if (sendResult.isSuccess()) {
					insertOrUpdateEventAsNotified(productEvenResulttDTO);
				} else {
					insertOrUpdateAsUnnotified(productEvenResulttDTO, sendResult);
				}
			} else {
				insertOrUpdateAsUnnotified(productEvenResulttDTO, null);
			}
		}

	}


private void insertOrUpdateAsUnnotified(CfpProductEventDTOResult productEventDTO, BillCFPSendEventDataResult sendEventDataResult) throws HmnServiceException {
		CfpEventLogFieldsDTO baseLogFields = productEventDTO.getBaseLogFields();

		PymBillCFPNotificationsDTO notificationDTO = QuerySupport.querySingleResult(NQConsts.GET_PYMCFP_BILL_NOTIFICATIONS.QUERY_NAME,
				new PymBillCFPNotificationsDTOTransformer(baseLogFields.getBillId(), baseLogFields.getTransactionStatu(), baseLogFields.getProduct(), baseLogFields.getInstitution()));

		if (notificationDTO == null) {
			insertAsUnnotified(productEventDTO, sendEventDataResult, baseLogFields);
		}

		else {
			updateAsUnnotified(productEventDTO, sendEventDataResult, notificationDTO);
		}

	}



private void insertNotificationAsNotified(CfpEventLogFieldsDTO baseLogFields) throws HmnException {
		

		IPymBillsCfpNotifications dataModel = BeanLocator.getPymBillsCfpNotifications();
		dataModel.setOid(Long.valueOf(baseLogFields.getRequestId()));
		dataModel.setSendDate(new Date());
		dataModel.setCreateDate(new Date());
		dataModel.setUpdateDate(new Date());
		dataModel.setProduct(baseLogFields.getProduct());
		dataModel.setInstitution(baseLogFields.getInstitution());
		dataModel.setSubscriberNo(baseLogFields.getSubscriberNo());
		dataModel.setLastPaymentDate(baseLogFields.getBillDueDate());
		dataModel.setBillNo(baseLogFields.getBillNo());
		dataModel.setBillId(new BigDecimal(baseLogFields.getBillId()));
		dataModel.setPaymentDate(baseLogFields.getPaymentDate());
		dataModel.setAmount(baseLogFields.getAmount());
		dataModel.setAccountingReference(PYMStringUtils.isEmptyString(baseLogFields.getAccountingRefNo()) ? null : Long.valueOf(baseLogFields.getAccountingRefNo()));
		dataModel.setCurrency(baseLogFields.getCurrency());
		dataModel.setClientNo(baseLogFields.getCustomerNo());
		dataModel.setFirmClientNo(baseLogFields.getFirmClientNo());
		dataModel.setCreditCardNo(baseLogFields.getCreditCardNo());
		dataModel.setCustomerAccountNo(baseLogFields.getCustomerAccountNo());
		dataModel.setFirmAccountNo(baseLogFields.getFirmAccountNo());
		dataModel.setStatus(baseLogFields.getTransactionStatu());
		dataModel.setIsProcessed(EnumYesNo.YES.getValue());
		dataModel.setVersion(BigDecimal.valueOf(1d));

		DaoLocator.getPymBillsCfpNotificationsDAO().create(dataModel);
		// QueryExecutor.getInstance().executeUpdate(NQConsts.PYM_CFP_SAVE_EVENTNOTIFICATIONS.QUERY_NAME, inputMap);
	}


	private void updateNotificationAsNotified(IPymBillsCfpNotifications record) throws HmnException {
		record.setSendDate(DateUtils.getCurrentDate());
		record.setIsProcessed(EnumYesNo.YES.getValue());
		record.setVersion(record.getVersion() == null ? BigDecimal.valueOf(1d) : record.getVersion().add(BigDecimal.valueOf(1d)));
		record.setErrorCode(null);
		record.setExceptionTrace("");
		record.setUpdateDate(DateUtils.getCurrentDate());

		DaoLocator.getPymBillsCfpNotificationsDAO().update(record);
	}
