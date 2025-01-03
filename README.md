    @Override
    public List<PaidBillLogDTO> getPaymentLogsByParameters(PaidBillLogRequestDTO requestDTO)
            throws MicroException {
        List<PaidBillDTO> billDTOS ;
        List<LogRecordDTO> logRecords;
        RequestCheckIsMicro requestCheckIsMicro= createRequest(requestDTO.getInstitution(), requestDTO.getProduct());

        if (facade.checkIsMicroInstitution(requestCheckIsMicro)) {
            RequestPaidBill billRequestDTO = mapper.prepareRequest(requestDTO);
            billDTOS = getPaymentsByParameters(billRequestDTO);
            logRecords = joinAndFilterLogRecords(requestDTO.getInstitution(),
                    requestDTO.getProduct(),
                    requestDTO.getServiceType(),
                    requestDTO.getReturnMapCode(),
                    requestDTO.getStartDate(),
                    requestDTO.getEndDate());

            return matchBillsWithLogs(billDTOS, logRecords);
        }
        RequestPaidBillLog billLogRequestDTO = mapper.prepareRequestSecond(requestDTO);

        return facade.getPaymentLogsByParameters(billLogRequestDTO).getPaidBillLogList();
    }
