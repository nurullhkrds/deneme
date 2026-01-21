	public static String generateUUID() {
		UUID uuid = UUID.randomUUID();
		String value = uuid.toString();
		SpringUtil.getBean(RequestContext.class).setDatapowerReferanceId(value);
		return value;
	}
