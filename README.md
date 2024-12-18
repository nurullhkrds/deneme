2022-01-12 00:00:00.0
String dueDateString = ObjectUtils.objToTargetType(map.get(NQConsts.GET_T_OTO_FATURA_FOR_LOG.OUT_DUE_DATE), String.class);
	            Date dueDate = dateFormat.parse(dueDateString);
