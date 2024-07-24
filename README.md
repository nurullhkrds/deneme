@Override
public DataResult<List<ReturnMapDTO>> searchReturnMaps(String returnMapCode, String bankReturnCode, String institutionErrorCode) {
    Specification<ReturnMap> spec = Specification.where(null);

    if (returnMapCode != null && !returnMapCode.isEmpty()) {
        spec = spec.and(ReturnMapCriteria.hasReturnMapCode(returnMapCode));
    }
    if (bankReturnCode != null && !bankReturnCode.isEmpty()) {
        spec = spec.and(ReturnMapCriteria.hasBankErrorCode(bankReturnCode));
    }
    if (institutionErrorCode != null && !institutionErrorCode.isEmpty()) {
        spec = spec.and(ReturnMapCriteria.hasInstitutionErrorCode(institutionErrorCode));
    }

    List<ReturnMap> returnMapList = returnMapRepository.findAll(spec);
    List<ReturnMapDTO> returnMapDTOList = returnMapMapper.toReturnMapDTOList(returnMapList);

    boolean success = !returnMapDTOList.isEmpty();
    String message = success ? "Results listed" : "No results found";
    int statusCode = success ? HttpStatus.OK.value() : HttpStatus.NOT_FOUND.value();

    return new DataResult<>(success, message, returnMapDTOList, statusCode);
}
