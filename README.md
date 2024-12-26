@Query(value = "SELECT inst.institution_code, inst.product_code, rm.return_map_code, rm.institution_return_text, " +
               "rm.bank_return_code " +
               "FROM INSTITUTION inst " +
               "CROSS JOIN RETURN_MAP rm " +
               "WHERE inst.institution_code = :institutionCode " +
               "AND inst.product_code = :productCode " +
               "AND rm.return_map_code = :returnMapCode",
       nativeQuery = true)
List<Object[]> findLogsByCriteriaNative(@Param("institutionCode") String institutionCode,
                                        @Param("productCode") String productCode,
                                        @Param("returnMapCode") String returnMapCode);
