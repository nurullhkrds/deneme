public class NotifyPaymentProcess extends AbstractProcess {

	private static final Integer MAX_LENGTH_OF_NEW_INFO_FIELDS = 100;
	private static final Integer MAX_LENGTH_OF_OLD_INFO_FIELD = 50;

	private Long notificationTryCount;
	private Long paymentNotificationId;
	private Long paymentId;

	private PaymentService paymentService;
	private PaymentNotificationService paymentNotificationService;
	private AdapterService adapterService;

	// be carefull for hibernate dirty update
	private PaymentNotification paymentNotification;
	// be carefull for hibernate dirty update
	private Payment payment;

	private NotifyPaymentAdapterResponse notifyPaymentResponse;

	@Override
	public void initProcess(ProcessExecutionInput input, ProcessLogDTO logDTO) {
		shouldRaiseExceptionOnABillError = false;
		super.initProcess(input, logDTO);
	}

	@Override
	protected void prepareExecutionOutput() {
		executionOutput = new NotifyPaymentProcessOutput();
		executionOutput.setResult(error);
		
		if(notifyPaymentResponse != null) {
			((NotifyPaymentProcessOutput) executionOutput).setInstitutionReturnCode(notifyPaymentResponse.getInstitutionResultCode());
			((NotifyPaymentProcessOutput) executionOutput).setInstitutionReturnCode(notifyPaymentResponse.getInstitutionResultDetail());
		}
	}

	@Override
	public void executeProcess() throws BillException {
		addProcessStep(new GatherData());
		addProcessStep(new FetchPaymentNotificationRecordWithLock());
		addProcessStep(new FetchPaymentRecordWithLock());
		addProcessStep(new CallInstitutionExternalService());
		addProcessStep(new UpdatePaymentNotificationRecord());
		addProcessStep(new UpdatePaymentRecord());
		addProcessStep(new DoAutomaticPaymentCancel());
		
		executeSteps();
		prepareLogVariable();
	}

	private class GatherData implements ProcessStep {

		@Override
		public void executeStep() throws BillException {
			notificationTryCount = Long.valueOf(Optional
					.ofNullable(SpringUtil.getBean(InstitutionFeatureService.class)
							.getFeatureValue(EnumFeatureCode.NOTIFICATION_TRY_COUNT, institutionCode, productCode))
					.orElse(BillTransactionConstant.DEFAULT_NOTIFICATION_TRY_COUNT));
			paymentNotificationId = (Long) dataPack.get(ProcessDataPackKey.PAYMENT_NOTIFICATION_ID.getKey());

			paymentService = SpringUtil.getBean(PaymentService.class);
			adapterService = SpringUtil.getBean(AdapterService.class);
			paymentNotificationService = SpringUtil.getBean(PaymentNotificationService.class);
		}

	}

	private class FetchPaymentNotificationRecordWithLock implements ProcessStep {

		@Override
		public void executeStep() throws BillException {
			paymentNotification = paymentNotificationService.findPaymentNotificationWithLock(paymentNotificationId);

			if (paymentNotification == null) {
				error = EnumBillResult.BILL_PAYMENT_NOTIFICATION_NOT_FOUND;
				return;
			}

			if (!EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION
					.equals(paymentNotification.getNotificationType())) {
				error = EnumBillResult.BILL_PAYMENT_NOTIFICATION_TYPE_INVALID;
				return;
			}

			if (EnumPaymentNotificationStatu.SUCCESS.equals(paymentNotification.getNotificationStatus())) {
				error = EnumBillResult.BILL_PAYMENT_NOTIFICATION_ALREADY_NOTIFIED;
				return;
			}

			Integer retryCount = Optional.ofNullable(paymentNotification.getRetryCount()).orElse(0);
			if (Integer.compare(retryCount, notificationTryCount.intValue()) > 0) {
				error = EnumBillResult.BILL_PAYMENT_NOTIFICATION_TRY_COUNT_EXCEEDED;
				return;
			}

			paymentId = paymentNotification.getPaymentId();

		}

	}

	private class FetchPaymentRecordWithLock implements ProcessStep {

		@Override
		public void executeStep() throws BillException {
			payment = paymentService.findPaymentByIdWithLock(paymentId);

			if (payment == null) {
				error = EnumBillResult.PAID_BILL_NOT_FOUND_ERROR;
				return;
			}
		}

	}

	private class CallInstitutionExternalService implements ProcessStep {

		@Override
		public void executeStep() throws BillException {

			NotifyPaymentAdapterRequest request = new NotifyPaymentAdapterRequest();
			request.setChannelCode(channelCode);
			request.setInstitution(institutionCode);
			request.setInstitutionDebtTypeId(payment.getInstitutionDebtTypeId());
			request.setInstitutionId(payment.getInstitutionDebtTypeId());
			request.setOperatingBranchCode(branchCode);
			request.setProduct(productCode);
			request.setRequestDate(LocalDateTime.now());
			request.setTransactionDate(payment.getCreateDate());
			request.setUserCode(agentCode);

			PaidBillAdapterDTO paidBillAdapterDTO = PaymentMapper.INSTANCE.toPaidBillAdapterDTO(payment);
			mapAdditionalInfoAreas(payment, paidBillAdapterDTO);

			request.setPaidBill(paidBillAdapterDTO);

			notifyPaymentResponse = adapterService.notifyPayment(request, payment.getChannelTransactionId(), payment.getChannelSessionId());
		}

		private void mapAdditionalInfoAreas(Payment payment, PaidBillAdapterDTO paidBillAdapterDTO) {

			String additionalInfo1 = StringUtils.rightPad(payment.getAdditionalInfo1(), MAX_LENGTH_OF_NEW_INFO_FIELDS);
			String additionalInfo2 = StringUtils.rightPad(payment.getAdditionalInfo2(), MAX_LENGTH_OF_NEW_INFO_FIELDS);
			String additionalInfo3 = StringUtils.rightPad(payment.getAdditionalInfo3(), MAX_LENGTH_OF_NEW_INFO_FIELDS);
			String additionalInfo4 = StringUtils.rightPad(payment.getAdditionalInfo4(), MAX_LENGTH_OF_NEW_INFO_FIELDS);
			String additionalInfo5 = StringUtils.rightPad(payment.getAdditionalInfo5(), MAX_LENGTH_OF_NEW_INFO_FIELDS);

			String info1 = StringUtils.substring(additionalInfo1, 0, MAX_LENGTH_OF_OLD_INFO_FIELD);
			String info2 = StringUtils.substring(additionalInfo2, 0, MAX_LENGTH_OF_OLD_INFO_FIELD);
			String info3 = StringUtils.substring(additionalInfo3, 0, MAX_LENGTH_OF_OLD_INFO_FIELD);
			String info4 = StringUtils.substring(additionalInfo4, 0, MAX_LENGTH_OF_OLD_INFO_FIELD);
			String info5 = StringUtils.substring(additionalInfo5, 0, MAX_LENGTH_OF_OLD_INFO_FIELD);

			String info6 = StringUtils.substring(additionalInfo1, MAX_LENGTH_OF_OLD_INFO_FIELD,	MAX_LENGTH_OF_NEW_INFO_FIELDS);
			String info7 = StringUtils.substring(additionalInfo2, MAX_LENGTH_OF_OLD_INFO_FIELD,	MAX_LENGTH_OF_NEW_INFO_FIELDS);
			String info8 = StringUtils.substring(additionalInfo3, MAX_LENGTH_OF_OLD_INFO_FIELD,	MAX_LENGTH_OF_NEW_INFO_FIELDS);
			String info9 = StringUtils.substring(additionalInfo4, MAX_LENGTH_OF_OLD_INFO_FIELD,	MAX_LENGTH_OF_NEW_INFO_FIELDS);

			paidBillAdapterDTO.setAdditionalInfo1(StringUtils.trim(info1));
			paidBillAdapterDTO.setAdditionalInfo2(StringUtils.trim(info2));
			paidBillAdapterDTO.setAdditionalInfo3(StringUtils.trim(info3));
			paidBillAdapterDTO.setAdditionalInfo4(StringUtils.trim(info4));
			paidBillAdapterDTO.setAdditionalInfo5(StringUtils.trim(info5));
			paidBillAdapterDTO.setAdditionalInfo6(StringUtils.trim(info6));
			paidBillAdapterDTO.setAdditionalInfo7(StringUtils.trim(info7));
			paidBillAdapterDTO.setAdditionalInfo8(StringUtils.trim(info8));
			paidBillAdapterDTO.setAdditionalInfo9(StringUtils.trim(info9));
		}

	}

	private class UpdatePaymentNotificationRecord implements ProcessStep {

		@Override
		public void executeStep() throws BillException {

			if (BillPaymentsConsts.RESPONSE_STATUS.SUCCESS.equals(notifyPaymentResponse.getStatus())) {
				paymentNotification.setNotificationStatus(EnumPaymentNotificationStatu.SUCCESS);
				paymentNotification.setNotificationDate(LocalDateTime.now());
			}

			else {
				paymentNotification.setNotificationStatus(EnumPaymentNotificationStatu.ERROR);
			}

			Integer retryCount = Optional.ofNullable(paymentNotification.getRetryCount()).orElse(0);
			paymentNotification.setRetryCount(retryCount + 1);
			paymentNotification.setResponseCode(notifyPaymentResponse.getInternalResultCode());
			paymentNotification.setResponseMessage(notifyPaymentResponse.getInternalResultDetail());
			paymentNotification.setInstitutionReturnCode(notifyPaymentResponse.getInstitutionResultCode());
			paymentNotification.setInstitutionReturnText(notifyPaymentResponse.getInstitutionResultDetail());
			paymentNotification.setUpdateDate(LocalDateTime.now());
			paymentNotification.setUpdatedBy("SYSTEM");
			paymentNotificationService.updatePaymentNotification(paymentNotification);
		}

	}

	private class UpdatePaymentRecord implements ProcessStep {

		@Override
		public void executeStep() throws BillException {
			NotifiedBillAdapterDTO notifiedBill = notifyPaymentResponse.getNotifiedBill();
			if (BillPaymentsConsts.RESPONSE_STATUS.SUCCESS.equals(notifyPaymentResponse.getStatus())
					&& notifiedBill != null) {
				payment.setPaymentStan(notifiedBill.getPaymentStan());
				payment.setInstitutionPaymentStan(notifiedBill.getInstitutionPaymentStan());
			}

			if (notifiedBill != null) {

				mapAdditionalInfoAreas(payment, notifiedBill);
			}

			paymentService.updatePayment(payment);
		}

		private void mapAdditionalInfoAreas(Payment payment, NotifiedBillAdapterDTO notifiedBill) {

			String additionalInfo1 = Optional.ofNullable(notifiedBill.getAdditionalInfo1()).orElse("");
			String additionalInfo2 = Optional.ofNullable(notifiedBill.getAdditionalInfo2()).orElse("");
			String additionalInfo3 = Optional.ofNullable(notifiedBill.getAdditionalInfo3()).orElse("");
			String additionalInfo4 = Optional.ofNullable(notifiedBill.getAdditionalInfo4()).orElse("");
			String additionalInfo5 = Optional.ofNullable(notifiedBill.getAdditionalInfo5()).orElse("");
			String additionalInfo6 = Optional.ofNullable(notifiedBill.getAdditionalInfo6()).orElse("");
			String additionalInfo7 = Optional.ofNullable(notifiedBill.getAdditionalInfo7()).orElse("");
			String additionalInfo8 = Optional.ofNullable(notifiedBill.getAdditionalInfo8()).orElse("");
			String additionalInfo9 = Optional.ofNullable(notifiedBill.getAdditionalInfo9()).orElse("");

			additionalInfo1 = StringUtils.rightPad(additionalInfo1, MAX_LENGTH_OF_OLD_INFO_FIELD);
			additionalInfo2 = StringUtils.rightPad(additionalInfo2, MAX_LENGTH_OF_OLD_INFO_FIELD);
			additionalInfo3 = StringUtils.rightPad(additionalInfo3, MAX_LENGTH_OF_OLD_INFO_FIELD);
			additionalInfo4 = StringUtils.rightPad(additionalInfo4, MAX_LENGTH_OF_OLD_INFO_FIELD);

			additionalInfo1 = additionalInfo1.concat(additionalInfo6);
			additionalInfo2 = additionalInfo2.concat(additionalInfo7);
			additionalInfo3 = additionalInfo3.concat(additionalInfo8);
			additionalInfo4 = additionalInfo4.concat(additionalInfo9);

			payment.setAdditionalInfo1(StringUtils.trim(additionalInfo1));
			payment.setAdditionalInfo2(StringUtils.trim(additionalInfo2));
			payment.setAdditionalInfo3(StringUtils.trim(additionalInfo3));
			payment.setAdditionalInfo4(StringUtils.trim(additionalInfo4));
			payment.setAdditionalInfo5(StringUtils.trim(additionalInfo5));
		}

	}

	private class DoAutomaticPaymentCancel implements ProcessStep {

		@Override
		public void executeStep() throws BillException {
			NotifiedBillAdapterDTO notifiedBill = notifyPaymentResponse.getNotifiedBill();

			if (notifyPaymentResponse.isReverseReqired()) {
				// TODO: implements later....
			}
		}

	}

	private void prepareLogVariable() {
		if (payment != null) {
			logDTO.setCustomerNo(payment.getCustomerNo());
			logDTO.setIdentityNo(payment.getIdentityNo());
			logDTO.setTaxId(payment.getTaxId());
			logDTO.setSubscriberNo(payment.getSubscriberNo());

			return;
		}

		if (paymentNotification != null) {
			PaymentDTO paymentDTO = Optional.ofNullable(paymentService.getPayment(paymentNotification.getPaymentId()))
					.orElse(new PaymentDTO());
			logDTO.setCustomerNo(paymentDTO.getCustomerNo());
			logDTO.setIdentityNo(paymentDTO.getIdentityNo());
			logDTO.setTaxId(paymentDTO.getTaxId());
			logDTO.setSubscriberNo(paymentDTO.getSubscriberNo());
		}

	}

}
