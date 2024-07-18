@DeleteMapping("/delete")
    @Transactional
    public ResponseEntity<String> deleteReturnMaps(@RequestParam List<Long> ids) {
        try {
            returnMapService.deleteReturnMaps(ids);
            return ResponseEntity.ok("Return maps deleted successfully");
        } catch (Exception e) {
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("Failed to delete return maps: " + e.getMessage());
        }
    }

    @PostMapping("/copy")
    @Transactional
    public ResponseEntity<String> copyReturnMaps(@RequestParam List<Long> ids, @RequestParam String copySuffix) {
        try {
            returnMapService.copyReturnMaps(ids, copySuffix);
            return ResponseEntity.ok("Return maps copied successfully");
        } catch (Exception e) {
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("Failed to copy return maps: " + e.getMessage());
        }
    }
