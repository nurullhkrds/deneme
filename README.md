@RestController
@RequestMapping("/returnMaps")
@RequiredArgsConstructor
@CrossOrigin(origins = "*", allowedHeaders = "*")

public class ReturnMapController {

    private final IReturnMapService returnMapService;

    private static final String X_TRACE_ID = "x-trace-id";
    private static final String X_SESSION_ID = "x-session-id";
    private final RequestContext requestContext;

    private void fillMandatoryFields(BaseWebRequest webRequest, String channelTransactionId, String channelSessionId) {
        requestContext.setChannelSessionId(channelSessionId);
        requestContext.setChannelTransactionId(channelTransactionId);
        requestContext.setAgentCode(webRequest.getAgentCode());
        requestContext.setChannelCode(webRequest.getChannelCode());
        requestContext.setOperatingBranchCode(webRequest.getOperatingBranchCode());
    }

    @GetMapping("/getAllByReturnMapCode")
    public ResponseEntity<DataResult<List<ReturnMapDTO>>> getAllByReturnMapCode(@RequestParam String returnMapCode) {
        DataResult<List<ReturnMapDTO>> result = returnMapService.getAllByReturnMapCode(returnMapCode);
        return ResponseEntity.status(result.getStatusCode()).body(result);
    }


    @GetMapping("/getAll")
    public ResponseEntity<DataResult<List<ReturnMapDTO>>> getAll() {
        DataResult<List<ReturnMapDTO>> result = returnMapService.getAll();
        return ResponseEntity.status(result.getStatusCode()).body(result);
    }

    @GetMapping("/getReturnMapById")
    public ResponseEntity<DataResult<ReturnMapDTO>> getReturnMapById(@RequestParam Long returnMapCodeId) {
        DataResult<ReturnMapDTO> result = returnMapService.getReturnMapById(returnMapCodeId);
        return ResponseEntity.status(result.getStatusCode()).body(result);
    }


    @PostMapping("/createReturnMap")
    public ResponseEntity<DataResult<ReturnMapDTO>> createReturnMap(
            @RequestBody CreateReturnMapRequest request)
            throws MicroException {
        DataResult<ReturnMapDTO> result = returnMapService.createReturnMap(request);
        return ResponseEntity.status(result.getStatusCode()).body(result);
    }


    @PutMapping("/updateReturnMap")
    public ResponseEntity<DataResult<ReturnMapDTO>> updateReturnMap(
            @RequestBody UpdateReturnMapRequest request)
            throws MicroException {
        DataResult<ReturnMapDTO> result = returnMapService.updateReturnMap(request);
        return ResponseEntity.status(result.getStatusCode()).body(result);
    }

    @GetMapping("/search")
    public ResponseEntity<DataResult<List<ReturnMapDTO>>> searchReturnMaps(
            @RequestParam(required = false) @Parameter(name = "returnMapCode") String returnMapCode,
            @RequestParam(required = false) @Parameter(name = "bankReturnCode") String bankReturnCode,
            @RequestParam(required = false) @Parameter(name = "institutionReturnCode") String institutionReturnCode) {
        DataResult<List<ReturnMapDTO>> searchResult = returnMapService.searchReturnMaps(returnMapCode, bankReturnCode, institutionReturnCode);
        return ResponseEntity.status(searchResult.getStatusCode()).body(searchResult);
    }



    @DeleteMapping("/delete")
    public ResponseEntity<Result> deleteReturnMaps(@RequestBody DeleteIdsRequest request) {
        Result result = returnMapService.deleteReturnMaps(request);
        return ResponseEntity.status(result.getStatusCode()).body(result);


    }

    @PostMapping("/copy")
    public ResponseEntity<Result> copyReturnMaps(@RequestBody CopyForIdsAndDataRequest request) {
        Result result = returnMapService.copyReturnMaps(request);
        return ResponseEntity.status(result.getStatusCode()).body(result);


    }


}
