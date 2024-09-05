    public static Specification<ReturnMap> hasReturnMapDefinitionCode(String returnMapCode) {
        return (Root<ReturnMap> root, CriteriaQuery<?> query, CriteriaBuilder cb) -> {
            if (returnMapCode == null || returnMapCode.isEmpty()) {
                return cb.conjunction();
            }
            Join<ReturnMap, ReturnMapDefinition> returnMapDefinitionJoin = root.join("returnMapDefinition", JoinType.LEFT);
            return cb.equal(cb.lower(returnMapDefinitionJoin.get("returnMapCode")), returnMapCode.toLowerCase());
        };
    }
