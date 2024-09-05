public static Specification<ReturnMap> hasReturnMapDefinitionCode(String returnMapCode) {
    return (Root<ReturnMap> root, CriteriaQuery<?> query, CriteriaBuilder cb) -> {
        if (returnMapCode == null || returnMapCode.isEmpty()) {
            return cb.conjunction(); // Eğer parametre boşsa, tüm sonuçları döndür
        }

        // ReturnMapDefinition ile INNER JOIN yaparak birleştiriyoruz
        Join<ReturnMap, ReturnMapDefinition> returnMapDefinitionJoin = root.join("returnMapDefinition", JoinType.INNER);

        // Gelen stringi küçük harfe çevirip, case-insensitive karşılaştırma yapıyoruz
        return cb.equal(cb.lower(returnMapDefinitionJoin.get("returnMapCode")), returnMapCode.toLowerCase());
    };
}
