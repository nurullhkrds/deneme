@Test
void testHasReturnMapDefinitionCode_withValidCode() {
    String returnMapCode = "MAP123";
    Root<ReturnMap> root = mock(Root.class);
    CriteriaQuery<?> query = mock(CriteriaQuery.class);
    CriteriaBuilder cb = mock(CriteriaBuilder.class);
    Join<ReturnMap, ReturnMapDefinition> join = mock(Join.class);

    // Correct stubbing with the actual parameters used in the code under test
    when(root.join(eq("returnMapDefinition"), eq(JoinType.INNER))).thenReturn(join);

    // Mock the upper case comparison instead of lower (matches the code)
    when(cb.upper(join.get("returnMapCode"))).thenReturn(mock(javax.persistence.criteria.Expression.class));
    when(cb.equal(any(javax.persistence.criteria.Expression.class), eq(returnMapCode.toUpperCase())))
            .thenReturn(mock(javax.persistence.criteria.Predicate.class));

    Specification<ReturnMap> spec = ReturnMapCriteria.hasReturnMapDefinitionCode(returnMapCode);
    spec.toPredicate(root, query, cb);

    // Verifying with the correct parameters
    verify(root).join("returnMapDefinition", JoinType.INNER);
    verify(cb).equal(any(javax.persistence.criteria.Expression.class), eq(returnMapCode.toUpperCase()));
}
