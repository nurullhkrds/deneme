    @DeleteMapping("/delete")
    public ResponseEntity<Result> deleteReturnMaps(@RequestParam List<Long> ids) {
        Result result = returnMapService.deleteReturnMaps(ids);
        return ResponseEntity.status(result.getStatusCode()).body(result);


    
