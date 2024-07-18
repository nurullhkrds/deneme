@Override
public DataResult<ReturnMapDTO> createReturnMap(CreateReturnMapRequest createReturnMapRequest) {
    // ReturnMap nesnesini oluştur ve alanlarını doldur
    ReturnMap returnMap = new ReturnMap();
    returnMap.setBankReturnCode(createReturnMapRequest.getBankReturnCode());
    returnMap.setBankReturnText(createReturnMapRequest.getBankReturnText());
    returnMap.setInstitutionReturnCode(createReturnMapRequest.getInstitutionReturnCode());
    returnMap.setInstitutionReturnText(createReturnMapRequest.getInstitutionReturnText());
    returnMap.setReturnType(createReturnMapRequest.getReturnType());
    returnMap.setIsReversible(createReturnMapRequest.getIsReversible());
    returnMap.setCreateDate(LocalDateTime.now());

    // ReturnMap nesnesini kaydet
    ReturnMap savedReturnMap = returnMapRepository.save(returnMap);

    // Kaydedilen ReturnMap nesnesini ReturnMapDTO'ya dönüştür
    ReturnMapDTO returnMapDTO = returnMapMapper.toReturnMapDTO(savedReturnMap);

    // DataResult nesnesini oluştur ve döndür
    boolean success = savedReturnMap != null;
    String message = success ? "Return map created successfully" : "Failed to create return map";
    int statusCode = success ? HttpStatus.CREATED.value() : HttpStatus.INTERNAL_SERVER_ERROR.value();

    return new DataResult<>(success, message, returnMapDTO, statusCode);
}
