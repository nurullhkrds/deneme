@Override
public DataResult<List<ReturnMapDTO>> getAllReturnMapList(String returnMapCode) {
    List<ReturnMap> returnMapList = returnMapRepository.findByReturnMapCode(returnMapCode);
    List<ReturnMapDTO> returnMapDTOList = returnMapMapper.toReturnMapDTOList(returnMapList);
    return new DataResult<>(returnMapDTOList, HttpStatus.OK.value());
}


@GetMapping("/getAllReturnMapList")
public ResponseEntity<DataResult<List<ReturnMapDTO>>> getAllReturnMapList(@RequestParam String returnMapCode) {
    DataResult<List<ReturnMapDTO>> result = returnMapService.getAllReturnMapList(returnMapCode);
    return ResponseEntity.status(result.getStatusCode()).body(result);
}
