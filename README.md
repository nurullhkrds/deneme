@Query(value = "SELECT i.name FROM INSTITUTION i " +
               "WHERE i.id IN (SELECT ia.institution_id " +
               "FROM INSTITUTION_ADAPTER ia " +
               "JOIN ADAPTER_SERVICE a ON ia.adapter_id = a.adapter_id " +
               "JOIN SERVICE s ON a.service_id = s.id " +
               "WHERE s.return_map_code = :returnMapCode)",
        nativeQuery = true)
List<String> findInstitutionNamesByReturnMapCode(@Param("returnMapCode") String returnMapCode);
