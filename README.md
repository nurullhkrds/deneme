01-FEB-24 11:05:55

	 SimpleDateFormat logDateFormat = new SimpleDateFormat("dd.MM.yy HH:mm:ss");

 String logDateString = ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_LOG_DATE), String.class);
	            Date logDate = logDateFormat.parse(logDateString);
	            dto.setLogDate(logDateFormat.format(logDate));
