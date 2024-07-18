{
  "exceptionData": {
    "applicationName": "PAYMENTS.BILL.bill-transaction",
    "errorCode": -995,
    "errorMessage": "Invalid request: JSON parse error: Input mismatch reading Enum `com.ykb.payments.bill.transaction.adapter.enums.EnumReturnType`: properties-based `@JsonCreator` ([method com.ykb.payments.bill.transaction.adapter.enums.EnumReturnType#getReturnType(java.lang.String)]) expects JSON Object (JsonToken.START_OBJECT), got JsonToken.VALUE_STRING; nested exception is com.fasterxml.jackson.databind.exc.MismatchedInputException: Input mismatch reading Enum `com.ykb.payments.bill.transaction.adapter.enums.EnumReturnType`: properties-based `@JsonCreator` ([method com.ykb.payments.bill.transaction.adapter.enums.EnumReturnType#getReturnType(java.lang.String)]) expects JSON Object (JsonToken.START_OBJECT), got JsonToken.VALUE_STRING\n at [Source: (org.springframework.util.StreamUtils$NonClosingInputStream); line: 7, column: 17] (through reference chain: com.ykb.payments.bill.transaction.adapter.web.request.CreateReturnMapRequest[\"returnType\"])",
    "traceId": null
  },
  "parameters": {}
}




  @PostMapping("/createReturnMap")
    public ResponseEntity<DataResult<ReturnMapDTO>> createReturnMap(/*@RequestHeader(value = X_TRACE_ID) String channelTransactionId,
                                                                 @RequestHeader(value = X_SESSION_ID) String channelSessionId,*/
            @RequestBody CreateReturnMapRequest request)
            throws MicroException {

/*
        fillMandatoryFields(request, channelTransactionId, channelSessionId);
*/
        DataResult<ReturnMapDTO> result = returnMapService.createReturnMap(request);
        return ResponseEntity.status(result.getStatusCode()).body(result);
    }


  @Transactional
    public DataResult<ReturnMapDTO> createReturnMap(CreateReturnMapRequest request) {
        ReturnMap returnMap = new ReturnMap();
        returnMap.setReturnMapCode(request.getReturnMapCode());
        returnMap.setInstitutionReturnCode(request.getInstitutionReturnCode());
        returnMap.setInstitutionReturnText(request.getInstitutionReturnText());
        returnMap.setBankReturnCode(request.getBankReturnCode());
        returnMap.setBankReturnText(request.getBankReturnText());
        returnMap.setReturnType(request.getReturnType());
        returnMap.setIsReversible(request.getIsReversible());

        ReturnMap result=returnMapRepository.save(returnMap);
        ReturnMapDTO resultDto=returnMapMapper.toReturnMapDTO(result);
        if (resultDto !=null){
            return new SuccessDataResult<>("ReturnMap Added",resultDto,200);
        }
        return new ErrorDataResult<>("ReturnMap not be added",null,400);
    }
