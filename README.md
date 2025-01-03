 @Override
    public List<PaidBillLogDTO> getPaymentLogsByParameters(PaidBillLogRequestDTO requestDTO)
            throws MicroException {
        List<PaidBillDTO> billDTOS ;
        List<LogRecordDTO> logRecords;
        String[] paymentTypeList = convertChannelCodesToPaymentTypes(requestDTO.getChannelCodes());
        RequestCheckIsMicro requestCheckIsMicro= createRequest(requestDTO.getInstitution(), requestDTO.getProduct());

        if (Boolean.TRUE.equals(facade.checkIsMicroInstitution(requestCheckIsMicro))) {
            RequestPaidBill billRequestDTO = mapper.prepareRequest(requestDTO);
            billRequestDTO.setPaymentTypeList(paymentTypeList);
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
        billLogRequestDTO.setPaymentTypeList(paymentTypeList);

        return facade.getPaymentLogsByParameters(billLogRequestDTO).getPaidBillLogList();
    }
