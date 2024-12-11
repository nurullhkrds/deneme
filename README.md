public CoreServiceResultDTO<List<PaidBillLogDTO>> getPaymentLogsByParameters(
    String institution, 
    String product, 
    List<String> subscriberList, 
    LocalDate startDate, 
    LocalDate endDate, 
    LocalDate paymentDate, 
    String billNo, 
    List<String> paymentTypeList
) throws HmnServiceException;
