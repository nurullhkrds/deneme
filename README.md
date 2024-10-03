@Query(value = "SELECT icpp.id, " +
        "icpp.inst_channel_pym_method_id, " +
        "icpp.MONDAY_BLOCK_DAY_COUNT, " +
        "icpp.TUESDAY_BLOCK_DAY_COUNT, " +
        "icpp.WEDNESDAY_BLOCK_DAY_COUNT, " +
        "icpp.THURSDAY_BLOCK_DAY_COUNT, " +
        "icpp.FRIDAY_BLOCK_DAY_COUNT, " +
        "icpp.SATURDAY_BLOCK_DAY_COUNT, " +
        "icpp.SUNDAY_BLOCK_DAY_COUNT, " +
        "icpp.created_by, " +       // Burada eksik olan virgül eklendi
        "icpp.create_date, " +      // Burada eksik olan virgül eklendi
        "icpp.updated_by, " +       // Burada eksik olan virgül eklendi
        "icpp.update_date, " +      // Burada eksik olan virgül eklendi
        "i.id AS institution_id, " +
        "i.name AS institution_name " +
        "FROM institution_chnnl_pym_mthd_psc icpp " +
        "JOIN institution_channel_pym_method icpm ON icpp.inst_channel_pym_method_id = icpm.id " +
        "JOIN institution_channel ic ON icpm.institution_channel_id = ic.id " +
        "JOIN institution_debt_type idt ON ic.institution_debt_type_id = idt.id " +
        "JOIN institution i ON idt.institution_id = i.id",
        nativeQuery = true)
List<Object[]> findInstitutionChnlPymMthdAccWithInstitution();
