@Tag(name = "Management ReturnMapDefinition BFF Controller")
@RequestMapping(RETURN_MAP_DEFINITION_PATH)
@RestController
public class ReturnMapDefinitionController {

    private final AdapterReturnMapDefinitionClient adapterReturnMapDefinitionClient;

    public ReturnMapDefinitionController(AdapterReturnMapDefinitionClient adapterReturnMapDefinitionClient) {
        this.adapterReturnMapDefinitionClient = adapterReturnMapDefinitionClient;
    }



    @PostMapping("/createReturnMapDefinition")
    public ResponseEntity<DataResult<ReturnMapDefinitionDTO>> createReturnMapDefinition(
            @RequestBody CreateReturnMapDefinitionRequest request) {
        DataResult<ReturnMapDefinitionDTO> result = adapterReturnMapDefinitionClient.createReturnMapDefinition(request);
        return ResponseEntity.status(result.getStatusCode()).body(result);
    }



@FeignClient(name = "PAYMENTS.BILL.bill-transaction", path = RETURN_MAP_DEFINITION_PATH, configuration = {MicroErrorDecoder.class})
public interface AdapterReturnMapDefinitionClient {


    @PostMapping("/createReturnMapDefinition")
    DataResult<ReturnMapDefinitionDTO> createReturnMapDefinition(@RequestBody CreateReturnMapDefinitionRequest request);







 @PostMapping("/createReturnMapDefinition")
    public ResponseEntity<DataResult<ReturnMapDefinitionDTO>> createReturnMapDefinition(
            @RequestBody CreateReturnMapDefinitionRequest request) {
        DataResult<ReturnMapDefinitionDTO> result = returnMapDefinitionService.createReturnMapDefinition(request);
        return ResponseEntity.status(result.getStatusCode()).body(result);
    }

 @Override
    public DataResult<ReturnMapDefinitionDTO> createReturnMapDefinition(CreateReturnMapDefinitionRequest request) {

        Optional<ReturnMapDefinition> existingReturnMap = returnMapDefinitionRepository.findByReturnMapCode(request.getReturnMapCode());

        if (existingReturnMap.isPresent()) {
            return new ErrorDataResult<>(ResultConstant.RECORD_ALREADY_EXISTS.getMessage(), null, 400);
        }

        ReturnMapDefinition newReturnMap = new ReturnMapDefinition();
        newReturnMap.setReturnMapCode(request.getReturnMapCode());
        newReturnMap.setIsActive(request.getIsActive());

        ReturnMapDefinition savedData = returnMapDefinitionRepository.save(newReturnMap);

        ReturnMapDefinitionDTO dto = returnMapDefinitionMapper.toReturnMapDefinitionDTO(savedData);

        if (dto == null) {
            return new ErrorDataResult<>(ResultConstant.CONVERSION_FAILED.getMessage(), null, 500);
        }

        return new SuccessDataResult<>(ResultConstant.SUCCESSFULLY_ADDED.getMessage(), dto,200);
    }




{
  "exceptionData": {
    "applicationName": "PAYMENTS.BILL.bill-bff",
    "errorCode": -999,
    "errorMessage": "Connection refused: no further information executing POST http://PAYMENTS.BILL.bill-transaction/returnMapDefinitions/createReturnMapDefinition",
    "traceId": null
  },
  "parameters": {}
}
