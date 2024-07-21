   @Transactional
    public void copyReturnMaps(List<Long> ids, String data) {
        List<ReturnMap> returnMapsToCopy = returnMapRepository.findAllById(ids);
        List<ReturnMap> copiedReturnMaps = new ArrayList<>();

        for (ReturnMap original : returnMapsToCopy) {
            ReturnMap copied = new ReturnMap();
            copied.setReturnMapCode(original.getReturnMapCode() + data);
            copied.setInstitutionReturnCode(original.getInstitutionReturnCode());
            copied.setInstitutionReturnText(original.getInstitutionReturnText());
            copied.setBankReturnCode(original.getBankReturnCode());
            copied.setBankReturnText(original.getBankReturnText());
            copied.setReturnType(original.getReturnType());
            copied.setIsReversible(original.getIsReversible());

            copiedReturnMaps.add(copied);
        }

        returnMapRepository.saveAll(copiedReturnMaps);
    }

    @PostMapping("/copy")
    public ResponseEntity<String> copyReturnMaps(@RequestParam List<Long> ids, @RequestParam String data) {
        try {
            returnMapService.copyReturnMaps(ids, data);
            return ResponseEntity.ok("Return maps copied successfully");
        } catch (Exception e) {
            return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body("Failed to copy return maps: " + e.getMessage());
        }
    } 

@Getter
@Setter
public class CopyForIdsAndData {
    private List<Long> ids;
    private String Data;
}
