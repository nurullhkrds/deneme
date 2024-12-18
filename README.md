@Override
	public PaidBillLogDTO convert(Map<String, Object> map) throws HmnServiceException {
		PaidBillLogDTO dto= new PaidBillLogDTO();
	
	
		dto.setInstitution(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_INSITUTION), String.class));
		dto.setProduct(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_PRODUCT), String.class));
		dto.setBillNo(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_BILL_NO), String.class));
		dto.setLogRecordNo(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_LOG_RECORD_NO), String.class));
		dto.setSubscriberNo(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_SUBSCRIBER_NO), String.class));
		dto.setInstitutionReturnCode(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_INSTITUTION_RETURN_CODE), String.class));
		dto.setInstitutionReturnText(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_INSTITUTION_RETURN_TEXT), String.class));
		dto.setTotalAmount(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_TOTAL_AMOUNT), BigDecimal.class));
		dto.setPaidAmount(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_PAID_AMOUNT), BigDecimal.class));
		dto.setPaymentType(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_PAYMENT_TYPE), String.class));
		dto.setReferenceNo(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_REFERENCE_NO), String.class));

		
		 try {
	            // dueDate processing
	            String dueDateString = ObjectUtils.objToTargetType(map.get(NQConsts.GET_T_OTO_FATURA_FOR_LOG.OUT_DUE_DATE), String.class);
	            Date dueDate = dateFormat.parse(dueDateString);
	            dto.setDueDate(outputDateFormat.format(dueDate));

	            // paymentDate processing
	            String paymentDateString = ObjectUtils.objToTargetType(map.get(NQConsts.GET_T_OTO_FATURA_FOR_LOG.OUT_PAYMENT_DATE), String.class);
	            Date paymentDate = dateFormat.parse(paymentDateString);
	            dto.setPaymentDate(outputDateFormat.format(paymentDate));

	            // logDate processing
	            String logDateString = ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_LOG_DATE), String.class);
	            Date logDate = logDateFormat.parse(logDateString);
	            dto.setLogDate(logDateFormat.format(logDate));

	        } catch (Exception e) {
	            throw new HmnServiceException("Date format error", e);
	        }
		return dto;
	}
