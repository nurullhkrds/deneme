public static Specification<ReturnMap> hasReturnMapDefinitionCode(String returnMapCode) {
    return (Root<ReturnMap> root, CriteriaQuery<?> query, CriteriaBuilder cb) -> {
        if (returnMapCode == null || returnMapCode.isEmpty()) {
            return cb.conjunction();
        }

        // INNER JOIN kullanarak sadece eşleşen kayıtları getiriyoruz
        Join<ReturnMap, ReturnMapDefinition> returnMapDefinitionJoin = root.join("returnMapDefinition", JoinType.INNER);

        return cb.equal(cb.lower(returnMapDefinitionJoin.get("returnMapCode")), returnMapCode.toLowerCase());
    };
}
