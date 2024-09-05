BELED%C4%B0YE_SILIVRI_ALL
public static Specification<ReturnMap> hasReturnMapDefinitionCode(String returnMapCode) {
    return (Root<ReturnMap> root, CriteriaQuery<?> query, CriteriaBuilder cb) -> {
        if (returnMapCode == null || returnMapCode.isEmpty()) {
            return cb.conjunction();
        }

        // ReturnMapDefinition ile INNER JOIN
        Join<ReturnMap, ReturnMapDefinition> returnMapDefinitionJoin = root.join("returnMapDefinition", JoinType.INNER);

        // "BELEDİYE_" gibi öneklerin özel karakter kaçışını sağlıyoruz
        String sanitizedCode = returnMapCode.replace("_", "\\_"); // _ karakterini kaçıyoruz

        // LIKE sorgusu ve ESCAPE kullanımı ile joker karakterin kaçışını sağlıyoruz
        return cb.like(cb.lower(returnMapDefinitionJoin.get("returnMapCode")), sanitizedCode.toLowerCase(), '\\');
    };
}
