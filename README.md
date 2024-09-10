@Test
void testHasReturnMapDefinitionCode_withValidCode() {
    String returnMapCode = "MAP123";
    Root<ReturnMap> root = mock(Root.class);
    CriteriaQuery<?> query = mock(CriteriaQuery.class);
    CriteriaBuilder cb = mock(CriteriaBuilder.class);
    Join<ReturnMap, ReturnMapDefinition> join = mock(Join.class);
    Path<Object> pathToReturnMapDefinition = mock(Path.class); // Mock the path to returnMapDefinition

    // Correct stubbing for root.join with actual parameters
    when(root.join(eq("returnMapDefinition"), eq(JoinType.INNER))).thenReturn(join);

    // Mock the return value of the get method on join and root
    when(join.get("returnMapCode")).thenReturn(mock(javax.persistence.criteria.Expression.class));
    when(cb.upper(any())).thenReturn(mock(javax.persistence.criteria.Expression.class));
    when(cb.equal(any(), eq(returnMapCode.toUpperCase()))).thenReturn(mock(javax.persistence.criteria.Predicate.class));

    // Mock root.get("returnMapDefinition") to return a mock object
    when(root.get("returnMapDefinition")).thenReturn(pathToReturnMapDefinition);
    when(pathToReturnMapDefinition.get("id")).thenReturn(mock(javax.persistence.criteria.Path.class));

    // Mock the CriteriaBuilder equal method for id comparison
    when(cb.equal(any(), any())).thenReturn(mock(javax.persistence.criteria.Predicate.class));

    // Execute the test
    Specification<ReturnMap> spec = ReturnMapCriteria.hasReturnMapDefinitionCode(returnMapCode);
    spec.toPredicate(root, query, cb);

    // Verify interactions
    verify(root).join("returnMapDefinition", JoinType.INNER);
    verify(cb).equal(any(), eq(returnMapCode.toUpperCase()));
}
