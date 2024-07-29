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
