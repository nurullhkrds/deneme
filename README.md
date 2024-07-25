public class ReturnMapCriteria {


    public static Specification<ReturnMap> hasReturnMapCode(String returnMapCode) {
        return (Root<ReturnMap> root, CriteriaQuery<?> query, CriteriaBuilder cb) -> {
            if (returnMapCode == null || returnMapCode.isEmpty()) {
                return cb.conjunction();
            }
            return cb.equal(root.get("returnMapCode"), returnMapCode);
        };
    }

    public static Specification<ReturnMap> hasBankErrorCode(String bankReturnCode) {
        return (Root<ReturnMap> root, CriteriaQuery<?> query, CriteriaBuilder cb) -> {
            if (bankReturnCode == null || bankReturnCode.isEmpty()) {
                return cb.conjunction();
            }
            return cb.equal(root.get("bankReturnCode"), bankReturnCode);
        };
    }

    public static Specification<ReturnMap> hasInstitutionErrorCode(String institutionReturnCode) {
        return (Root<ReturnMap> root, CriteriaQuery<?> query, CriteriaBuilder cb) -> {
            if (institutionReturnCode == null || institutionReturnCode.isEmpty()) {
                return cb.conjunction();
            }
            return cb.equal(root.get("institutionReturnCode"), institutionReturnCode);
        };
    }

}
