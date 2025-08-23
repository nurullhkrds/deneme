// Tek metot, Java 8, temiz ve net.
// - limitCheckEnabled=false → sadece amount geçerlilik kontrolü yapılır (null/<=0 ise hata).
// - limitCheckEnabled=true  → amount + min/max limit kontrolleri yapılır.
// Hata durumunda EnumResponseCodes döner ve varsa errorMessageParams doldurulur.
private EnumResponseCodes checkPaymentLimits(
        final boolean limitCheckEnabled,
        final BigDecimal paymentAmount,
        final BigDecimal minLimit,
        final BigDecimal maxLimit,
        final Map<String, String> errorMessageParams) {

    // 1) Amount zorunlu ve pozitif olmalı
    if (paymentAmount == null || paymentAmount.compareTo(BigDecimal.ZERO) <= 0) {
        if (errorMessageParams != null) {
            errorMessageParams.put(EnumResponseCodes.MSGPARAM_KEYS_INPUT_NAME,
                                   EnumResponseCodes.MSGPARAM_VALUES_PAYMENT_AMOUNT);
            errorMessageParams.put(EnumResponseCodes.MSGPARAM_KEYS_INPUT_VALUE,
                                   paymentAmount == null ? "null" : paymentAmount.toPlainString());
        }
        return EnumResponseCodes.INSTITUTION_PAYMENT_AMOUNT_INVALID;
    }

    // 2) Limit kontrolü kapalı ise biter
    if (!limitCheckEnabled) {
        return null;
    }

    // 3) Üst limit (payment > max)
    if (maxLimit != null && paymentAmount.compareTo(maxLimit) > 0) {
        if (errorMessageParams != null) {
            errorMessageParams.put(EnumResponseCodes.MSGPARAM_KEYS_INPUT_NAME,
                                   EnumResponseCodes.MSGPARAM_VALUES_PAYMENT_AMOUNT);
            errorMessageParams.put(EnumResponseCodes.MSGPARAM_KEYS_INPUT_VALUE,
                                   paymentAmount.toPlainString());
            errorMessageParams.put(EnumResponseCodes.MSGPARAM_KEYS_UPPER_VALUE,
                                   maxLimit.toPlainString());
        }
        return EnumResponseCodes.INSTITUTION_PAYMENT_UPPER_LIMIT_EXCEED;
    }

    // 4) Alt limit (payment < min)
    if (minLimit != null && paymentAmount.compareTo(minLimit) < 0) {
        if (errorMessageParams != null) {
            errorMessageParams.put(EnumResponseCodes.MSGPARAM_KEYS_INPUT_NAME,
                                   EnumResponseCodes.MSGPARAM_VALUES_PAYMENT_AMOUNT);
            errorMessageParams.put(EnumResponseCodes.MSGPARAM_KEYS_INPUT_VALUE,
                                   paymentAmount.toPlainString());
            errorMessageParams.put(EnumResponseCodes.MSGPARAM_KEYS_LOWER_VALUE,
                                   minLimit.toPlainString());
        }
        return EnumResponseCodes.INSTITUTION_PAYMENT_LOWER_LIMIT_EXCEED;
    }

    // 5) Sorun yok
    return null;
}


Map<String, String> errorMessageParams = new HashMap<>();

EnumResponseCodes error = checkPaymentLimits(
        isCheckPaymentLimit,                         // boolean switch
        pPaymentAmount,                              // gelen ödeme tutarı
        billProvisionDTO.getMinPaymentAmount(),      // kurumun alt limiti
        billProvisionDTO.getMaxPaymentAmount(),      // kurumun üst limiti
        errorMessageParams                           // hata parametreleri doldurulacak map
);

if (error != null) {
    // Hata kodu setleniyor
    errorCode = error.getValue();

    // errorMessageParams map'i burada dolmuş olacak
    // Log atabilir ya da response'a ekleyebilirsin
    log.warn("Payment validation failed. code={}, params={}", errorCode, errorMessageParams);

    return; // hatadan dolayı işlemi bitir
}

