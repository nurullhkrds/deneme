public static Specification<ReturnMap> hasReturnMapCode(String returnMapCode) {
        return (Root<ReturnMap> root, CriteriaQuery<?> query, CriteriaBuilder cb) -> {
            if (returnMapCode == null || returnMapCode.isEmpty()) {
                return cb.conjunction();
            }
            String pattern = "%" + returnMapCode.toLowerCase() + "%";
            Expression<String> returnMapCodeField = cb.lower(root.get("returnMapCode"));
            return cb.like(returnMapCodeField, pattern);
        };
    }

    public static Specification<ReturnMap> hasBankErrorCode(String bankReturnCode) {
        return (Root<ReturnMap> root, CriteriaQuery<?> query, CriteriaBuilder cb) -> {
            if (bankReturnCode == null || bankReturnCode.isEmpty()) {
                return cb.conjunction();
            }
            String pattern = "%" + bankReturnCode.toLowerCase() + "%";
            Expression<String> bankErrorCodeField = cb.lower(root.get("bankReturnCode"));
            return cb.like(bankErrorCodeField, pattern);
        };
    }

    public static Specification<ReturnMap> hasInstitutionErrorCode(String institutionReturnCode) {
        return (Root<ReturnMap> root, CriteriaQuery<?> query, CriteriaBuilder cb) -> {
            if (institutionReturnCode == null || institutionReturnCode.isEmpty()) {
                return cb.conjunction();
            }
            String pattern = "%" + institutionReturnCode.toLowerCase() + "%";
            Expression<String> institutionErrorCodeField = cb.lower(root.get("institutionReturnCode"));
            return cb.like(institutionErrorCodeField, pattern);
        };
    }
