@Override
    public DataResult<ReturnMap> createReturnMap(CreateReturnMapRequest createReturnMapRequest) {
        // ReturnMap nesnesini oluştur ve alanlarını doldur
        ReturnMap returnMap = new ReturnMap();
        returnMap.setBankReturnCode(createReturnMapRequest.getBankReturnCode());
        returnMap.setBankReturnText(createReturnMapRequest.getBankReturnText());
        returnMap.setInstitutionReturnCode(createReturnMapRequest.getInstitutionReturnCode());
        returnMap.setInstitutionReturnText(createReturnMapRequest.getInstitutionReturnText());
        returnMap.setReturnType(createReturnMapRequest.getReturnType());
        returnMap.setIsReversible(createReturnMapRequest.getIsReversible());
        returnMap.setCreateDate(LocalDateTime.now());

        ReturnMap savedReturnMap = returnMapRepository.save(returnMap);

        ReturnMapDTO returnMapDTO = returnMapMapper.toReturnMapDTO(savedReturnMap);

        boolean success = savedReturnMap != null;
        String message = success ? "Return map created successfully" : "Failed to create return map";
        int statusCode = success ? HttpStatus.CREATED.value() : HttpStatus.INTERNAL_SERVER_ERROR.value();

        return new DataResult<>(success, message, returnMap, statusCode);
    }

   @PostMapping("/createReturnMap")
    public ResponseEntity<DataResult<ReturnMap>> createReturnMap(@RequestBody CreateReturnMapRequest createReturnMapRequest){
        DataResult<ReturnMap> result=returnMapService.createReturnMap(createReturnMapRequest);
        return ResponseEntity.status(result.getStatusCode()).body(result);
    }
