
private EnumResponseCodes validatePaymentLimits(
        boolean limitCheckEnabled,
        BigDecimal paymentAmount,
        BigDecimal minLimit,
        BigDecimal maxLimit,
        Map<String, String> errorMessageParams) {

    // 1) Payment null veya sıfırdan küçük/eşitse hata
    if (paymentAmount == null || paymentAmount.compareTo(BigDecimal.ZERO) <= 0) {
        putParams(errorMessageParams,
                EnumResponseCodes.MSGPARAM_VALUES_PAYMENT_AMOUNT,
                safeToString(paymentAmount), null, null);
        return EnumResponseCodes.INSTITUTION_PAYMENT_AMOUNT_INVALID;
    }

    // 2) Limit kontrolü kapalıysa geç
    if (!limitCheckEnabled) {
        return null;
    }

    // 3) Üst limit kontrolü
    if (maxLimit != null && paymentAmount.compareTo(maxLimit) > 0) {
        putParams(errorMessageParams,
                EnumResponseCodes.MSGPARAM_VALUES_PAYMENT_AMOUNT,
                paymentAmount.toPlainString(),
                maxLimit.toPlainString(),
                null);
        return EnumResponseCodes.INSTITUTION_PAYMENT_UPPER_LIMIT_EXCEED;
    }

    // 4) Alt limit kontrolü
    if (minLimit != null && paymentAmount.compareTo(minLimit) < 0) {
        putParams(errorMessageParams,
                EnumResponseCodes.MSGPARAM_VALUES_PAYMENT_AMOUNT,
                paymentAmount.toPlainString(),
                null,
                minLimit.toPlainString());
        return EnumResponseCodes.INSTITUTION_PAYMENT_LOWER_LIMIT_EXCEED;
    }

    // 5) Sorun yok
    return null;
}

private void putParams(Map<String, String> params,
                       String inputValue,
                       String paymentValue,
                       String upperValue,
                       String lowerValue) {
    if (params == null) {
        return;
    }
    params.put(EnumResponseCodes.MSGPARAM_KEYS_INPUT_NAME, inputValue);
    params.put(EnumResponseCodes.MSGPARAM_KEYS_INPUT_VALUE, paymentValue);
    if (upperValue != null) {
        params.put(EnumResponseCodes.MSGPARAM_KEYS_UPPER_VALUE, upperValue);
    }
    if (lowerValue != null) {
        params.put(EnumResponseCodes.MSGPARAM_KEYS_LOWER_VALUE, lowerValue);
    }
}

private String safeToString(BigDecimal value) {
    return value == null ? "null" : value.toPlainString();
}


EnumResponseCodes error = validatePaymentLimits(
        isCheckPaymentLimit,
        pPaymentAmount,
        billProvisionDTO.getMinPaymentAmount(),
        billProvisionDTO.getMaxPaymentAmount(),
        errorMessageParams == null ? new HashMap<>() : errorMessageParams
);

if (error != null) {
    errorCode = error.getValue();
    return;
}
