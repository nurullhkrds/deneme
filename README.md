
    @Test
    void testHasReturnMapDefinitionCode_withValidCode() {
        String returnMapCode = "MAP123";
        Root<ReturnMap> root = mock(Root.class);
        CriteriaQuery<?> query = mock(CriteriaQuery.class);
        CriteriaBuilder cb = mock(CriteriaBuilder.class);
        Join<ReturnMap, ReturnMapDefinition> join = mock(Join.class);  // Join nesnesini mockluyoruz.

        // Burada root.join metodundan dönen değeri casting ile Join türüne dönüştürüyoruz.
        when(root.join(eq("returnMapDefinition"), eq(JoinType.LEFT))).thenReturn((Join) join); 

        when(cb.lower(join.get("returnMapCode"))).thenReturn(mock(javax.persistence.criteria.Expression.class));
        when(cb.equal(any(javax.persistence.criteria.Expression.class), eq(returnMapCode.toLowerCase())))
                .thenReturn(mock(javax.persistence.criteria.Predicate.class));

        Specification<ReturnMap> spec = ReturnMapCriteria.hasReturnMapDefinitionCode(returnMapCode);
        spec.toPredicate(root, query, cb);

        verify(root).join("returnMapDefinition", JoinType.LEFT);
        verify(cb).equal(any(javax.persistence.criteria.Expression.class), eq(returnMapCode.toLowerCase()));
    }
