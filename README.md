@Test
void testHasReturnMapDefinitionCode_withValidCode() {
    String returnMapCode = "MAP123";
    Root<ReturnMap> root = mock(Root.class);
    CriteriaQuery<?> query = mock(CriteriaQuery.class);
    CriteriaBuilder cb = mock(CriteriaBuilder.class);
    Join<ReturnMap, ReturnMapDefinition> join = mock(Join.class);
    Path<String> returnMapCodePath = mock(Path.class); // Mock Path<String> for returnMapCode
    Path<Object> pathToReturnMapDefinition = mock(Path.class); // Mock the path to returnMapDefinition

    // Correct stubbing for root.join with actual parameters
    when(root.join(eq("returnMapDefinition"), eq(JoinType.INNER))).thenReturn(join);

    // Mock the return value of the get method on join to return Path<String> for returnMapCode
    when(join.get("returnMapCode")).thenReturn(returnMapCodePath);

    // Mock CriteriaBuilder's upper method to work with Path<String>
    Expression<String> upperExpression = mock(Expression.class);
    when(cb.upper(returnMapCodePath)).thenReturn(upperExpression);

    // Mock root.get("returnMapDefinition") to return a mock object
    when(root.get("returnMapDefinition")).thenReturn(pathToReturnMapDefinition);
    Path<Object> idPath = mock(Path.class);
    when(pathToReturnMapDefinition.get("id")).thenReturn(idPath);

    // Mock the CriteriaBuilder equal method for id and returnMapCode comparisons
    Predicate predicateReturnMapCode = mock(Predicate.class);
    Predicate predicateIdMatch = mock(Predicate.class);
    when(cb.equal(upperExpression, returnMapCode.toUpperCase())).thenReturn(predicateReturnMapCode);
    when(cb.equal(root.get("returnMapDefinition").get("id"), join.get("id"))).thenReturn(predicateIdMatch);

    // Execute the test
    Specification<ReturnMap> spec = ReturnMapCriteria.hasReturnMapDefinitionCode(returnMapCode);
    spec.toPredicate(root, query, cb);

    // Verify interactions
    verify(root).join("returnMapDefinition", JoinType.INNER);
    verify(cb).equal(upperExpression, returnMapCode.toUpperCase()); // Match the actual arguments
    verify(cb).equal(root.get("returnMapDefinition").get("id"), join.get("id"));
}
