    @GetMapping("/getAllInstitutions")
    public ResponseEntity<DataResult<List<InstitutionWebDTO>>> getAllInstitutions() {
        DataResult<List<InstitutionDTO>> result = institutionService.getAllInstitutions();
        List<InstitutionWebDTO> institutionWebDTOList = institutionMapper.toInstitutionWebDTOList(result.getData());
        return ResponseEntity.status(result.getStatusCode()).body((DataResult<List<InstitutionWebDTO>>) institutionWebDTOList);
    }
