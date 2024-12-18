  try {
            String dueDateString = ObjectUtils.objToTargetType(map.get(NQConsts.GET_T_OTO_FATURA_FOR_LOG.OUT_DUE_DATE), String.class);
            Date dueDate = dateFormat.parse(dueDateString);
            dto.setDueDate(dateFormat.format(dueDate));

            String paymentDateString = ObjectUtils.objToTargetType(map.get(NQConsts.GET_T_OTO_FATURA_FOR_LOG.OUT_PAYMENT_DATE), String.class);
            Date paymentDate = dateFormat.parse(paymentDateString);
            dto.setPaymentDate(dateFormat.format(paymentDate));

            String logDateString = ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_LOG_DATE), String.class);
            Date logDate = logDateFormat.parse(logDateString);
            dto.setLogDate(logDateFormat.format(logDate));
        } catch (Exception e) {
            PYMErrorUtils.throwGenericErrorServiceResult("getPaymentLogsByParameters", e.getClass().getSimpleName(), "GetPaymentLogsTransformer", "date");     
        }
