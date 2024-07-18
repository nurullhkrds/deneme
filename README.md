   @DeleteMapping("/delete")
    @Transactional
    public ResponseEntity<String> deleteReturnMaps(@RequestParam List<Long> ids) {
        returnMapService.deleteReturnMaps(ids);
        return ResponseEntity.ok("Return maps deleted successfully");
    }

    @PostMapping("/copy")
    @Transactional
    public ResponseEntity<String> copyReturnMaps(@RequestParam List<Long> ids, @RequestParam String copySuffix) {
        returnMapService.copyReturnMaps(ids, copySuffix);
        return ResponseEntity.ok("Return maps copied successfully");
    }
