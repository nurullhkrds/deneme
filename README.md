    @Query(value = "SELECT i.* FROM INSTITUTION i " +
            "WHERE i.id IN (SELECT ia.institution_id " +
            "FROM INSTITUTION_ADAPTER ia " +
            "JOIN ADAPTER_SERVICE as ON ia.adapter_id = as.adapter_id " +
            "JOIN SERVICE s ON as.service_id = s.id " +
            "WHERE s.return_map_code = :returnMapCode)",
            nativeQuery = true)
    List<Institution> findInstitutionsByReturnMapCode(@Param("returnMapCode") String returnMapCode);

ate: SELECT i.* FROM INSTITUTION i WHERE i.id IN (SELECT ia.institution_id FROM INSTITUTION_ADAPTER ia JOIN ADAPTER_SERVICE as ON ia.adapter_id = as.adapter_id JOIN SERVICE s ON as.service_id = s.id WHERE s.return_map_code = ?)
2024-08-23 23:01:06,511 WARN [http-nio-8081-exec-4][SqlExceptionHelper] SQL Error: 905, SQLState: 42000
2024-08-23 23:01:06,511 ERROR [http-nio-8081-exec-4][SqlExceptionHelper] ORA-00905: eksik anahtar sözcük

2024-08-23 23:01:06,522 ERROR [http-nio-8081-exec-4][PymExceptionHandler] An Exception occured org.springframework.dao.InvalidDataAccessResourceUsageException: could not extract ResultSet; SQL [n/a]; nested exception is org.hibernate.exception.SQLGrammarException: could not extract ResultSet
