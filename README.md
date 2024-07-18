    @Override
    public DataResult<ReturnMapDTO> createReturnMap(CreateReturnMapRequest createReturnMapRequest) {
        ReturnMap returnMap=new ReturnMap();
        returnMap.setBankReturnCode(createReturnMapRequest.getBankReturnCode());
        returnMap.setBankReturnText(createReturnMapRequest.getBankReturnText());
        returnMap.setInstitutionReturnCode(createReturnMapRequest.getInstitutionReturnCode());
        returnMap.setInstitutionReturnText(createReturnMapRequest.getInstitutionReturnText());
        returnMap.setReturnType(createReturnMapRequest.getReturnType());
        returnMap.setBankReturnCode(createReturnMapRequest.getBankReturnCode());
        returnMap.setIsReversible(createReturnMapRequest.getIsReversible());
        returnMap.setCreateDate(LocalDateTime.now());
        ReturnMap saved=returnMapRepository.save(returnMap);
        ReturnMapDTO returnMapDTO=returnMapMapper.toReturnMapDTO(returnMap);
       
        return null;
    }
