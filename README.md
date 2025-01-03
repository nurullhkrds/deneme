@Override
public List<PaidBillLogDTO> getPaymentLogsByParameters(PaidBillLogRequestDTO requestDTO)
        throws MicroException {
    List<PaidBillDTO> billDTOS;
    List<LogRecordDTO> logRecords;

    // Channel kodlarını dönüştür
    String[] paymentTypeList = convertChannelCodesToPaymentTypes(requestDTO.getChannelCodes());
    
    // Micro kurum kontrolü
    RequestCheckIsMicro requestCheckIsMicro = createRequest(requestDTO.getInstitution(), requestDTO.getProduct());
    
    if (Boolean.TRUE.equals(facade.checkIsMicroInstitution(requestCheckIsMicro))) {
        // Micro kurum için işlem
        RequestPaidBill billRequestDTO = mapper.prepareRequest(requestDTO);
        billRequestDTO.setPaymentTypeList(paymentTypeList);
        
        billDTOS = getPaymentsByParameters(billRequestDTO);
        logRecords = joinAndFilterLogRecords(
                requestDTO.getInstitution(),
                requestDTO.getProduct(),
                requestDTO.getServiceType(),
                requestDTO.getReturnMapCode(),
                requestDTO.getStartDate(),
                requestDTO.getEndDate()
        );
        
        return matchBillsWithLogs(billDTOS, logRecords);
    }

    // Micro olmayan kurum için işlem
    RequestPaidBillLog billLogRequestDTO = mapper.prepareRequestSecond(requestDTO);
    billLogRequestDTO.setPaymentTypeList(paymentTypeList);
    
    return facade.getPaymentLogsByParameters(billLogRequestDTO).getPaidBillLogList();
}
