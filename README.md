@RestController
@RequestMapping("/returnMapDefinitions")
public class ReturnMapDefinitionController {

    private final IReturnMapDefinitionService returnMapDefinitionService;

    public ReturnMapDefinitionController(IReturnMapDefinitionService returnMapDefinitionService) {
        this.returnMapDefinitionService = returnMapDefinitionService;
    }


   @GetMapping("getAllReturnMapDefinitionReturnMapWithIsActiveTrue")
    public ResponseEntity<DataResult<List<ReturnMapDefinitionDTO>>> getAllReturnMapDefinitionReturnMapWithIsActiveTrue() {
        DataResult<List<ReturnMapDefinitionDTO>> result = returnMapDefinitionService.getAllReturnMapDefinitionReturnMapWithIsActiveTrue();
        return ResponseEntity.status(result.getStatusCode()).body(result);
    }
