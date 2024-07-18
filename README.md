@Override
public DataResult<List<ReturnMapDTO>> getAllReturnMapList(String returnMapCode) {
    List<ReturnMap> returnMapList = returnMapRepository.findByReturnMapCode(returnMapCode);
    List<ReturnMapDTO> returnMapDTOList = returnMapMapper.toReturnMapDTOList(returnMapList);

    // DataResult constructor'larına uygun şekilde
    boolean success = !returnMapDTOList.isEmpty();
    String message = success ? "Data retrieved successfully" : "No data found";
    int statusCode = success ? HttpStatus.OK.value() : HttpStatus.NO_CONTENT.value();

    return new DataResult<>(success, message, returnMapDTOList, statusCode);
}
