   @GetMapping("/search")
    public ResponseEntity<DataResult<List<ReturnMapDTO>>> searchReturnMaps(
            @RequestParam(required = false) String returnMapCode,
            @RequestParam(required = false) String bankReturnCode,
            @RequestParam(required = false) String institutionErrorCode) {
        DataResult<List<ReturnMapDTO>> searhResult = returnMapService.searchReturnMaps(returnMapCode, bankReturnCode, institutionErrorCode);
        return ResponseEntity.status(searhResult.getStatusCode()).body(searhResult);
    }

 @Override
    public DataResult<List<ReturnMapDTO>> searchReturnMaps(String returnMapCode, String bankReturnCode, String institutionErrorCode) {
        Specification<ReturnMap> spec = Specification.where(ReturnMapCriteria.hasReturnMapCode(returnMapCode))
                .and(ReturnMapCriteria.hasBankErrorCode(bankReturnCode))
                .and(ReturnMapCriteria.hasInstitutionErrorCode(institutionErrorCode));

        Sort sort = Sort.by(Sort.Direction.ASC, "returnMapCode");


        List<ReturnMap> returnMapList=returnMapRepository.findAll(spec, sort);
        List<ReturnMapDTO> returnMapDTOList=returnMapMapper.toReturnMapDTOList(returnMapList);
        return new SuccessDataResult<>("Results listed",returnMapDTOList,200);

    }
