    @GetMapping("/search")
    public ResponseEntity<DataResult<List<ReturnMapDTO>>> searchReturnMaps(
            @RequestParam(required = false) @Parameter(name = "returnMapCode") String returnMapCode,
            @RequestParam (required = false) String institutionReturnCode,
            @RequestParam(required = false) @Parameter(name = "bankReturnCode") String bankReturnCode
           ) {
        DataResult<List<ReturnMapDTO>> searchResult = adapterReturnMapClient.searchReturnMaps(returnMapCode, bankReturnCode, institutionReturnCode);
        System.out.println("returnmap: "+returnMapCode );
        System.out.println("bankCode: "+bankReturnCode);
        System.out.println("kurum: "+institutionReturnCode);

        return ResponseEntity.status(searchResult.getStatusCode()).body(searchResult);
    }
