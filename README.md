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
