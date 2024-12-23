  @Override
    public List<PaidBillLogDTO> getPaymentLogsByParameters(PaidBillLogRequestDTO requestDTO) {
        List<PaidBillDTO> billDTOS ;
        List<LogRecordDTO> logRecords;
        if (Boolean.TRUE.equals(requestDTO.getIsMicro())) {
            RequestPaidBill billRequestDTO = mapper.prepareRequest(requestDTO);
            billDTOS = getPaymentsByParameters(billRequestDTO);
            logRecords = getFilteredLogRecords(
                    requestDTO.getInstitution(),
                    requestDTO.getProduct(),
                    requestDTO.getReturnMapCode(),
                    requestDTO.getStartDate(),
                    requestDTO.getEndDate()
            );

            return matchBillsWithLogs(billDTOS, logRecords);
        }
        RequestPaidBillLog billLogRequestDTO = mapper.prepareRequestSecond(requestDTO);

        return facade.getPaymentLogsByParameters(billLogRequestDTO).getPaidBillLogList();
    }
