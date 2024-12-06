List<String> validResultCodes = Arrays.asList("620", "621", "622", "623", "624", "625");

if (BillPaymentsConsts.RESPONSE_STATUS.SUCCESS.equals(response.getStatus()) || 
    validResultCodes.contains(response.getInternalResultCode())) {
    
    response.setTotalPaymentAmount(mutabakatResult.value.getToplamOdemeTutari());
    response.setTotalPaymentCount(mutabakatResult.value.getToplamOdemeSayisi().intValue());
    response.setTotalPaymentCancelAmount(mutabakatResult.value.getIptalOdemeTutari());
    response.setTotalPaymentCancelCount(mutabakatResult.value.getIptalOdemeSayisi().intValue());
}
