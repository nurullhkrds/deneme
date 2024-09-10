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
    Expression<String> returnMapCodeExpression = mock(Expression.class);
    when(join.get("returnMapCode")).thenReturn(returnMapCodeExpression);
    when(cb.upper(returnMapCodeExpression)).thenReturn(mock(Expression.class));

    // Mock root.get("returnMapDefinition") to return a mock object
    when(root.get("returnMapDefinition")).thenReturn(pathToReturnMapDefinition);
    Path<Object> idPath = mock(Path.class);
    when(pathToReturnMapDefinition.get("id")).thenReturn(idPath);

    // Mock the CriteriaBuilder equal method for id and returnMapCode comparisons
    when(cb.equal(any(), any())).thenReturn(mock(javax.persistence.criteria.Predicate.class));

    // Execute the test
    Specification<ReturnMap> spec = ReturnMapCriteria.hasReturnMapDefinitionCode(returnMapCode);
    spec.toPredicate(root, query, cb);

    // Verify interactions
    verify(root).join("returnMapDefinition", JoinType.INNER);
    verify(cb).equal(any(), eq(returnMapCode.toUpperCase()));
}
