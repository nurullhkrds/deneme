   @Override
    public DataResult<List<ReturnMapDTO>> searchReturnMaps(String returnMapCode, String bankReturnCode, String institutionReturnCode) {
        Specification<ReturnMap> spec = Specification.where(null);

        if (returnMapCode != null && !returnMapCode.isEmpty()) {
            spec = spec.and(ReturnMapCriteria.hasReturnMapCode(returnMapCode));
        }
        if (bankReturnCode != null && !bankReturnCode.isEmpty()) {
            spec = spec.and(ReturnMapCriteria.hasBankErrorCode(bankReturnCode));
        }
        if (institutionReturnCode != null && !institutionReturnCode.isEmpty()) {
            spec = spec.and(ReturnMapCriteria.hasInstitutionErrorCode(institutionReturnCode));
        }

        List<ReturnMap> returnMapList = returnMapRepository.findAll(spec);
        List<ReturnMapDTO> returnMapDTOList = returnMapMapper.toReturnMapDTOList(returnMapList);
        if (returnMapDTOList.isEmpty()){
            List<ReturnMapDTO> returnMapDTOList1=new ArrayList<>();
            new ErrorDataResult<>("Listed is empty",returnMapDTOList1,400);
        }
        return new SuccessDataResult<>("Result listed", returnMapDTOList, 200);
    }


    @GetMapping("/search")
    public ResponseEntity<DataResult<List<ReturnMapDTO>>> searchReturnMaps(
            @RequestParam(required = false) @Parameter(name = "returnMapCode") String returnMapCode,
            @RequestParam(required = false) @Parameter(name = "bankReturnCode") String bankReturnCode,
            @RequestParam(required = false) @Parameter(name = "institutionReturnCode") String institutionReturnCode) {
        DataResult<List<ReturnMapDTO>> searhResult = returnMapService.searchReturnMaps(returnMapCode,bankReturnCode,institutionReturnCode);
        return ResponseEntity.status(searhResult.getStatusCode()).body(searhResult);
    }

 public static Specification<ReturnMap> hasReturnMapCode(String returnMapCode) {
        return (Root<ReturnMap> root, CriteriaQuery<?> query, CriteriaBuilder cb) -> {
            if (returnMapCode == null) {
                return cb.conjunction();
            }
            return cb.equal(root.get("returnMapCode"), returnMapCode);
        };
    }

    public static Specification<ReturnMap> hasBankErrorCode(String bankReturnCode) {
        return (Root<ReturnMap> root, CriteriaQuery<?> query, CriteriaBuilder cb) -> {
            if (bankReturnCode == null) {
                return cb.conjunction();
            }
            return cb.equal(root.get("bankReturnCode"), bankReturnCode);
        };
    }

    public static Specification<ReturnMap> hasInstitutionErrorCode(String institutionReturnCode) {
        return (Root<ReturnMap> root, CriteriaQuery<?> query, CriteriaBuilder cb) -> {
            if (institutionReturnCode == null) {
                return cb.conjunction();
            }
            return cb.equal(root.get("institutionReturnCode"), institutionReturnCode);
        };
    }

   @Override
    public DataResult<List<ReturnMapDTO>> searchReturnMaps(String returnMapCode, String bankReturnCode, String institutionReturnCode) {
        Specification<ReturnMap> spec = Specification.where(null);

        if (returnMapCode != null && !returnMapCode.isEmpty()) {
            spec = spec.and(ReturnMapCriteria.hasReturnMapCode(returnMapCode));
        }
        if (bankReturnCode != null && !bankReturnCode.isEmpty()) {
            spec = spec.and(ReturnMapCriteria.hasBankErrorCode(bankReturnCode));
        }
        if (institutionReturnCode != null && !institutionReturnCode.isEmpty()) {
            spec = spec.and(ReturnMapCriteria.hasInstitutionErrorCode(institutionReturnCode));
        }

        List<ReturnMap> returnMapList = returnMapRepository.findAll(spec);
        List<ReturnMapDTO> returnMapDTOList = returnMapMapper.toReturnMapDTOList(returnMapList);
        if (returnMapDTOList.isEmpty()){
            List<ReturnMapDTO> returnMapDTOList1=new ArrayList<>();
            new ErrorDataResult<>("Listed is empty",returnMapDTOList1,400);
        }
        return new SuccessDataResult<>("Result listed", returnMapDTOList, 200);
    }
