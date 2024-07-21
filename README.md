@PostMapping("/copy")
public ResponseEntity<String> copyReturnMaps(@RequestBody CopyForIdsAndData request) {
    String result = returnMapService.copyReturnMaps(request);
    if (result.startsWith("Başarısız")) {
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(result);
    } else {
        return ResponseEntity.ok(result);
    }
}
