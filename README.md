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
