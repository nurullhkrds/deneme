public PaidBillDTO convert(Map<String, Object> map) throws HmnServiceException {
        PaidBillDTO dto = new PaidBillDTO();
        SimpleDateFormat logDateFormat = new SimpleDateFormat("dd.MM.yy HH:mm:ss");
        SimpleDateFormat outputDateFormat = new SimpleDateFormat("dd.MM.yyyy");

        dto.setInstitution(ObjectUtils.objToTargetType(map.get(NQConsts.GET_T_OTO_FATURA_FOR_LOG.OUT_INSITUTION), String.class));
        dto.setProduct(ObjectUtils.objToTargetType(map.get(NQConsts.GET_T_OTO_FATURA_FOR_LOG.OUT_PRODUCT), String.class));
        dto.setBillNo(ObjectUtils.objToTargetType(map.get(NQConsts.GET_T_OTO_FATURA_FOR_LOG.OUT_BILL_NO), String.class));
        dto.setSubscriberNo(ObjectUtils.objToTargetType(map.get(NQConsts.GET_T_OTO_FATURA_FOR_LOG.OUT_SUBSCRIBER_NO), String.class));
        dto.setTotalAmount(ObjectUtils.objToTargetType(map.get(NQConsts.GET_T_OTO_FATURA_FOR_LOG.OUT_TOTAL_AMOUNT), BigDecimal.class));
        dto.setPaidAmount(ObjectUtils.objToTargetType(map.get(NQConsts.GET_T_OTO_FATURA_FOR_LOG.OUT_PAID_AMOUNT), BigDecimal.class));

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

        } catch (ParseException e) {
            throw new HmnServiceException("Date format error", e);
        }

        dto.setPaymentType(ObjectUtils.objToTargetType(map.get(NQConsts.GET_T_OTO_FATURA_FOR_LOG.OUT_PAYMENT_TYPE), String.class));
        dto.setReferenceNo(ObjectUtils.objToTargetType(map.get(NQConsts.GET_T_OTO_FATURA_FOR_LOG.OUT_REFERENCE_NO), String.class));

        return dto;
    }
