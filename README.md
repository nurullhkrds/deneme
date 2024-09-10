
    public static Specification<ReturnMap> hasReturnMapDefinitionCode(String returnMapCode) {
        return (Root<ReturnMap> root, CriteriaQuery<?> query, CriteriaBuilder cb) -> {
            if (returnMapCode == null || returnMapCode.isEmpty()) {
                return cb.conjunction(); 
            }

            Join<ReturnMap, ReturnMapDefinition> returnMapDefinitionJoin = root.join("returnMapDefinition", JoinType.INNER);

            Predicate returnMapCodeMatch = cb.equal(cb.upper(returnMapDefinitionJoin.get("returnMapCode")), returnMapCode.toUpperCase());

            Predicate idMatch = cb.equal(root.get("returnMapDefinition").get("id"), returnMapDefinitionJoin.get("id"));

            return cb.and(returnMapCodeMatch, idMatch);
        };
    }    

@Test
    void testHasReturnMapDefinitionCode_withValidCode() {
        String returnMapCode = "MAP123";
        Root<ReturnMap> root = mock(Root.class);
        CriteriaQuery<?> query = mock(CriteriaQuery.class);
        CriteriaBuilder cb = mock(CriteriaBuilder.class);
        Join<ReturnMap, ReturnMapDefinition> join = mock(Join.class);

        when(root.join(eq("returnMapDefinition"), eq(JoinType.INNER))).thenReturn((Join) join);

        when(cb.lower(join.get("returnMapCode"))).thenReturn(mock(javax.persistence.criteria.Expression.class));
        when(cb.equal(any(javax.persistence.criteria.Expression.class), eq(returnMapCode.toLowerCase())))
                .thenReturn(mock(javax.persistence.criteria.Predicate.class));

        Specification<ReturnMap> spec = ReturnMapCriteria.hasReturnMapDefinitionCode(returnMapCode);
        spec.toPredicate(root, query, cb);

        verify(root).join("returnMapDefinition", JoinType.LEFT);
        verify(cb).equal(any(javax.persistence.criteria.Expression.class), eq(returnMapCode.toLowerCase()));
    }


org.mockito.exceptions.misusing.PotentialStubbingProblem: 
Strict stubbing argument mismatch. Please check:
 - this invocation of 'join' method:
    root.join("returnMapDefinition", INNER);
    -> at com.ykb.payments.bill.transaction.adapter.criteria.ReturnMapCriteria.lambda$hasReturnMapDefinitionCode$269e2a48$1(ReturnMapCriteria.java:22)
 - has following stubbing(s) with different arguments:
    1. root.join(null, null);
      -> at com.ykb.payments.bill.transaction.adapter.criteria.ReturnMapCriteriaTest.testHasReturnMapDefinitionCode_withValidCode(ReturnMapCriteriaTest.java:33)
Typically, stubbing argument mismatch indicates user mistake when writing tests.
Mockito fails early so that you can debug potential problem easily.
However, there are legit scenarios when this exception generates false negative signal:
  - stubbing the same method multiple times using 'given().will()' or 'when().then()' API
    Please use 'will().given()' or 'doReturn().when()' API for stubbing.
  - stubbed method is intentionally invoked with different arguments by code under test
    Please use default or 'silent' JUnit Rule (equivalent of Strictness.LENIENT).
For more information see javadoc for PotentialStubbingProblem class.
