
    @Override
    public DataResult<ReturnMapDTO> getReturnMapById(Long returnMapCodeId) {
        Optional<ReturnMap> returnMapOptional=returnMapRepository.findById(returnMapCodeId);
        if (returnMapOptional.isPresent()){
            ReturnMapDTO dto=returnMapMapper.toReturnMapDTO(returnMapOptional.get());
            return new SuccessDataResult<>("result found",dto,200 );
        }
        return new ErrorDataResult<>("result not found !",null,400);
    }

    @Override
    public DataResult<List<ReturnMapDTO>> searchReturnMaps(String returnMapCode, String bankReturnCode, String institutionReturnCode) {
        Specification<ReturnMap> spec = Specification.where(null);

        if (returnMapCode != null && !returnMapCode.isEmpty()) {
            spec = spec.and(ReturnMapCriteria.hasReturnMapCode(returnMapCode));
        }
        if (bankReturnCode != null && !bankReturnCode.isEmpty()) {
            spec = spec.and(ReturnMapCriteria.hasBankErrorCode(bankReturnCode));
        }
        if (institutionReturnCode != null && !institutionReturnCode.isEmpty()) {
            spec = spec.and(ReturnMapCriteria.hasInstitutionErrorCode(institutionReturnCode));
        }

        List<ReturnMap> returnMapList = returnMapRepository.findAll(spec);
        List<ReturnMapDTO> returnMapDTOList = returnMapMapper.toReturnMapDTOList(returnMapList);

        if (returnMapDTOList.isEmpty()) {
            return new ErrorDataResult<>("Listed is empty", returnMapDTOList, 200);
        }

        return new SuccessDataResult<>("Result listed", returnMapDTOList, 200);
    }



    private boolean isNotBlank(String value) {
        return value != null && !value.trim().isEmpty();
    }



    public DataResult<List<ReturnMapDTO>> getAllByReturnMapCode(String returnMapCode) {
        List<ReturnMap> returnMapList = returnMapRepository.findByReturnMapCode(returnMapCode);
        List<ReturnMapDTO> returnMapDTOList = returnMapMapper.toReturnMapDTOList(returnMapList);

        boolean success = !returnMapDTOList.isEmpty();
        String message = success ? "Data retrieved successfully" : "No data found";
        int statusCode = success ? HttpStatus.OK.value() : HttpStatus.NOT_FOUND.value();

        return new DataResult<>(success, message, returnMapDTOList, statusCode);
    }

