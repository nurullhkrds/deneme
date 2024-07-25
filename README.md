public final class ProcessConstant {

	public static List<Class<? extends IProcess>> getProcessClassList() {
		return List.of(QueryBillsProcess.class, BillPaymentProcess.class, BillPaymentReverseProcess.class,
				NotifyPaymentProcess.class, NotifyPaymentCancelProcess.class);
	}

	public static class ProcessParameterKey {
		public static final String KEY_CHANNEL_CODE = "CHANNEL_CODE";
		public static final String KEY_START_TIME = "START_TIME";
		public static final String KEY_FINISH_TIME = "FINISH_TIME";
		public static final String KEY_ID = "ID";
	}

}
