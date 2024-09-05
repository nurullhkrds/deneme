public static Specification<ReturnMap> hasReturnMapDefinitionCode(String returnMapCode) {
    return (Root<ReturnMap> root, CriteriaQuery<?> query, CriteriaBuilder cb) -> {
        if (returnMapCode == null || returnMapCode.isEmpty()) {
            return cb.conjunction(); // Eğer parametre boşsa, tüm sonuçları döndür
        }

        // ReturnMapDefinition ile INNER JOIN yaparak id eşleşmesini sağlıyoruz
        Join<ReturnMap, ReturnMapDefinition> returnMapDefinitionJoin = root.join("returnMapDefinition", JoinType.INNER);

        // Eşleşme işlemi: return_map_definition_id == id
        Predicate idMatch = cb.equal(root.get("returnMapDefinitionId"), returnMapDefinitionJoin.get("id"));

        // Gelen stringi küçük harfe çevirip, case-insensitive karşılaştırma yapıyoruz
        Predicate returnMapCodeMatch = cb.equal(cb.lower(returnMapDefinitionJoin.get("returnMapCode")), returnMapCode.toLowerCase());

        // İki şartı birleştirip dönüyoruz
        return cb.and(idMatch, returnMapCodeMatch);
    };
}
