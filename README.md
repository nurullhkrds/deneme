 "errorMessage": "Type definition error: [simple type, class com.ykb.payments.bill.common.utilities.DataResult]; nested exception is com.fasterxml.jackson.databind.exc.InvalidDefinitionException: Cannot construct instance of `com.ykb.payments.bill.common.utilities.DataResult` (no Creators, like default constructor, exist): cannot deserialize from Object value (no delegate- or property-based Creator)\n at [Source: (org.springframework.util.StreamUtils$NonClosingInputStream); line: 1, column: 2]",


    @PostMapping("/getPaymentLogsByParameters")
    public ResponseEntity<DataResult<List<PaidBillLogWebDTO>>> getPaymentLogsByParameters(
            @RequestBody @Valid PaidBillLogRequest request) throws MicroException {

        PaidBillLogRequestDTO requestDTO= mapper.toRequestDTO(request);
        List<PaidBillLogDTO> dtoList=paidBillLogService.getPaymentLogsByParameters(requestDTO);
        List<PaidBillLogWebDTO> webDTOList=mapper.toWebDTOList(dtoList);
        DataResult<List<PaidBillLogWebDTO>> resultDTO = new DataResult<>
                (ResultConstant.DATA_LISTED.getMessage(),webDTOList);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }

bu veriyi gönderdiğim yer...


    @PostMapping("/getPaymentLogsByParameters")
    public ResponseEntity<DataResult<List<PaidBillLogWebDTO>>> getPaymentLogsByParameters(
            @RequestBody PaidBillLogRequest request) throws MicroException {

        DataResult<List<PaidBillLogWebDTO>> result = client.getPaymentLogsByParameters(request);
        DataResult<List<PaidBillLogWebDTO>> resultDTO = new DataResult<>
                (result.getMessage(),result.getData());
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }
bu aldığım yer 


@FeignClient(name = "PAYMENTS.BILL.bill-transaction", path = PAID_BILL_LOGS_PATH, configuration = {MicroErrorDecoder.class})
public interface AdapterLogClient {

    @PostMapping("/getPaymentLogsByParameters")
    DataResult<List<PaidBillLogWebDTO>> getPaymentLogsByParameters(
            @RequestBody @Valid PaidBillLogRequest request) throws MicroException; 
bu da feign client
