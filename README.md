	public static String generateUUID() {
		UUID uuid = UUID.randomUUID();
		String value = uuid.toString();
		SpringUtil.getBean(RequestContext.class).setDatapowerReferanceId(value);
		return value;
	}
com.ykb.payments.bill.common.dto.RequestContext.setDatapowerReferanceId(String)" because the return value of "com.ykb.payments.bill.common.util.SpringUtil.getBean(java.lang.Class)" is null
