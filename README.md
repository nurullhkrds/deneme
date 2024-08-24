@RestController
@RequestMapping("/returnMapDefinitions")
public class ReturnMapDefinitionController {

    private final IReturnMapDefinitionService returnMapDefinitionService;

    public ReturnMapDefinitionController(IReturnMapDefinitionService returnMapDefinitionService) {
        this.returnMapDefinitionService = returnMapDefinitionService;
    }


    @GetMapping("getAllReturnMapDefinitionReturnMap")
    public ResponseEntity<DataResult<List<ReturnMapDefinitionDTO>>> getAllReturnMapDefinitionReturnMap() {
        DataResult<List<ReturnMapDefinitionDTO>> result = returnMapDefinitionService.getAllReturnMapDefinitionReturnMap();
        return ResponseEntity.status(result.getStatusCode()).body(result);
    }
    @GetMapping("getReturnMapDefinitionByReturnMapCode")
    public ResponseEntity<DataResult<ReturnMapDefinitionDTO>> getReturnMapDefinitionByReturnMapCode(@RequestParam String returnMapCode) {
        DataResult<ReturnMapDefinitionDTO> result = returnMapDefinitionService.getReturnMapDefinitionByReturnMapCode(returnMapCode);
        return ResponseEntity.status(result.getStatusCode()).body(result);
    }

    @GetMapping("/getReturnMapDefinitionById")
    public ResponseEntity<DataResult<ReturnMapDefinitionDTO>> getReturnMapDefinitionById(@RequestParam Long id) {
        DataResult<ReturnMapDefinitionDTO> result = returnMapDefinitionService.getReturnMapDefinitionById(id);
        return ResponseEntity.status(result.getStatusCode()).body(result);
    }


    @PostMapping("/createReturnMapDefinition")
    public ResponseEntity<DataResult<ReturnMapDefinitionDTO>> createReturnMapDefinition(
            @RequestBody CreateReturnMapDefinitionRequest request) {
        DataResult<ReturnMapDefinitionDTO> result = returnMapDefinitionService.createReturnMapDefinition(request);
        return ResponseEntity.status(result.getStatusCode()).body(result);
    }


    @PutMapping("/updateReturnMapDefinition")
    public ResponseEntity<DataResult<ReturnMapDefinitionDTO>> updateReturnMapDefinition(
            @RequestBody UpdateReturnMapDefinitionRequest request) {
        DataResult<ReturnMapDefinitionDTO> result = returnMapDefinitionService.updateReturnMapDefinition(request);
        return ResponseEntity.status(result.getStatusCode()).body(result);
    }

    @DeleteMapping("/delete")
    public ResponseEntity<Result> deleteReturnMapDefinitions(@RequestBody DeleteIdsRequest request) {
        Result result = returnMapDefinitionService.deleteReturnMapDefinitions(request);
        return ResponseEntity.status(result.getStatusCode()).body(result);


    }
}
