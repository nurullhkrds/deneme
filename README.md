    @PutMapping("/updateInstitution")
    public ResponseEntity<DataResult<AdapterInstitutionDTO>> updateInstitution(@RequestBody UpdateInstitutionRequest request) throws MicroException {
        DataResult<AdapterInstitutionDTO> result = adapterInstitutionService.updateInstitution(request);
        return ResponseEntity.status(result.getStatusCode()).body(result);
    }
