    @Query(value = "SELECT icpp.id, " +
            "icpp.inst_channel_pym_method_id, " +
            "icpp.MONDAY_BLOCK_DAY_COUNT, " +
            "icpp.TUESDAY_BLOCK_DAY_COUNT, " +
            "icpp.WEDNESDAY_BLOCK_DAY_COUNT, " +
            "icpp.THURSDAY_BLOCK_DAY_COUNT, " +
            "icpp.FRIDAY_BLOCK_DAY_COUNT, " +
            "icpp.SATURDAY_BLOCK_DAY_COUNT, " +
            "icpp.SUNDAY_BLOCK_DAY_COUNT, " +
            "icpp.created_by"+
            "icpp.create_date"+
            "icpp.updated_by"+
            "icpp.update_date"+
            "i.id AS institution_id, " +
            "i.name AS institution_name " +
            "FROM institution_chnnl_pym_mthd_psc icpp " +
            "JOIN institution_channel_pym_method icpm ON icpp.inst_channel_pym_method_id = icpm.id " +
            "JOIN institution_channel ic ON icpm.institution_channel_id = ic.id " +
            "JOIN institution_debt_type idt ON ic.institution_debt_type_id = idt.id " +
            "JOIN institution i ON idt.institution_id = i.id",
            nativeQuery = true)
    List<Object[]> findInstitutionChnlPymMthdAccWithInstitution();




Hibernate: SELECT icpp.id, icpp.inst_channel_pym_method_id, icpp.MONDAY_BLOCK_DAY_COUNT, icpp.TUESDAY_BLOCK_DAY_COUNT, icpp.WEDNESDAY_BLOCK_DAY_COUNT, icpp.THURSDAY_BLOCK_DAY_COUNT, icpp.FRIDAY_BLOCK_DAY_COUNT, icpp.SATURDAY_BLOCK_DAY_COUNT, icpp.SUNDAY_BLOCK_DAY_COUNT, icpp.created_byicpp.create_dateicpp.updated_byicpp.update_datei.id AS institution_id, i.name AS institution_name FROM institution_chnnl_pym_mthd_psc icpp JOIN institution_channel_pym_method icpm ON icpp.inst_channel_pym_method_id = icpm.id JOIN institution_channel ic ON icpm.institution_channel_id = ic.id JOIN institution_debt_type idt ON ic.institution_debt_type_id = idt.id JOIN institution i ON idt.institution_id = i.id
2024-10-03 17:39:04,448 WARN [http-nio-8080-exec-2][SqlExceptionHelper] SQL Error: 904, SQLState: 42000
2024-10-03 17:39:04,449 ERROR [http-nio-8080-exec-2][SqlExceptionHelper] ORA-00904: "ICPP"."CREATED_BYICPP"."CREATE_DATEICPP"."UPDATED_BYICPP"."UPDATE_DATEI"."ID": geçersiz belirleyici

2024-10-03 17:39:04,479 ERROR [http-nio-8080-exec-2][PymExceptionHandler] An Exception occured org.springframework.dao.InvalidDataAccessResourceUsageException: could not extract ResultSet; SQL [n/a]; nested exception is org.hibernate.exception.SQLGrammarException: could not extract ResultSet
