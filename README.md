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
        when(root.join(eq("returnMapDefinition"), eq(JoinType.INNER))).thenReturn((Join)join);

        // Mock the return value of the get method on join to return Path<String> for returnMapCode
        when(join.get("returnMapCode")).thenReturn((Path)returnMapCodePath);

        // Mock CriteriaBuilder's upper method to work with Path<String>
        when(cb.upper(returnMapCodePath)).thenReturn(mock(Expression.class));

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


org.mockito.exceptions.misusing.PotentialStubbingProblem: 
Strict stubbing argument mismatch. Please check:
 - this invocation of 'equal' method:
    criteriaBuilder.equal(
    Mock for Expression, hashCode: 678801430,
    "MAP123"
);
    -> at com.ykb.payments.bill.transaction.adapter.criteria.ReturnMapCriteria.lambda$hasReturnMapDefinitionCode$269e2a48$1(ReturnMapCriteria.java:23)
 - has following stubbing(s) with different arguments:
    1. criteriaBuilder.equal(null, null);
      -> at com.ykb.payments.bill.transaction.adapter.criteria.ReturnMapCriteriaTest.testHasReturnMapDefinitionCode_withValidCode(ReturnMapCriteriaTest.java:45)
Typically, stubbing argument mismatch indicates user mistake when writing tests.
Mockito fails early so that you can debug potential problem easily.
However, there are legit scenarios when this exception generates false negative signal:
  - stubbing the same method multiple times using 'given().will()' or 'when().then()' API
    Please use 'will().given()' or 'doReturn().when()' API for stubbing.
  - stubbed method is intentionally invoked with different arguments by code under test
    Please use default or 'silent' JUnit Rule (equivalent of Strictness.LENIENT).
For more information see javadoc for PotentialStubbingProblem class.
