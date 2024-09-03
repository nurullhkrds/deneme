    @GetMapping("getReturnMapDefinitionByReturnMapCode")
    public ResponseEntity<DataResult<ReturnMapDefinitionDTO>> getReturnMapDefinitionByReturnMapCode(@RequestParam String returnMapCode) {
        DataResult<ReturnMapDefinitionDTO> result = returnMapDefinitionService.getReturnMapDefinitionByReturnMapCode(returnMapCode);
        String escapedReturnMapCode = StringEscapeUtils.escapeHtml(returnMapCode);
        DataResult<ReturnMapDefinitionDTO> result = returnMapDefinitionService.getReturnMapDefinitionByReturnMapCode(escapedReturnMapCode);
        return ResponseEntity.status(result.getStatusCode()).body(result);
    }
