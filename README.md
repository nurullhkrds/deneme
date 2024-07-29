 @Test
    void hasReturnMapCode_whenCodeIsNull_shouldReturnConjunction() {
        Specification<ReturnMap> spec = ReturnMapSpecifications.hasReturnMapCode(null);
        when(cb.conjunction()).thenReturn(predicate);
        Predicate result = spec.toPredicate(root, query, cb);
        assertThat(result).isEqualTo(predicate);
    }
