String responseInternalResultCode;
if (responseList != null && !responseList.isEmpty()) {
    responseInternalResultCode = "0";
} else {
    responseInternalResultCode = EnumResponseCodes.GENERIC__UNKNOWN_ERROR.getValue(); // Hata kodu belirtebilirsiniz.
}
