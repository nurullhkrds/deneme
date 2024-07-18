 public List<ReturnMap> searchReturnMaps(String returnMapCode, String aaErrorCode, String institutionErrorCode) {
        Specification<ReturnMap> spec = Specification.where(ReturnMapSpecification.hasReturnMapCode(returnMapCode))
                .and(ReturnMapSpecification.hasAaErrorCode(aaErrorCode))
                .and(ReturnMapSpecification.hasInstitutionErrorCode(institutionErrorCode));
        return returnMapRepository.findAll(spec);
    }
