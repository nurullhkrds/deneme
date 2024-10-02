@Query(value = "SELECT icpa.id, " +
               "icpa.inst_channel_pym_method_id, " +
               "icpa.collection_account_no, " +
               "icpa.institution_account_no, " +
               "icpa.currency, " +
               "icpa.expense_type, " +
               "icpa.expense_account_no, " +
               "icpa.is_active, " +
               "i.id AS institution_id, " +
               "i.name AS institution_name " +
               "FROM institution_chnnl_pym_mthd_acc icpa " +
               "JOIN institution_channel_pym_method icpm ON icpa.inst_channel_pym_method_id = icpm.id " +
               "JOIN institution_channel ic ON icpm.institution_channel_id = ic.id " +
               "JOIN institution_debt_type idt ON ic.institution_debt_type_id = idt.id " +
               "JOIN institution i ON idt.institution_id = i.id", 
       nativeQuery = true)
List<Object[]> findInstitutionChnlPymMthdAccWithInstitution();
public InstitutionChnlPymMthdAccWebDTO toWebDTO(Object[] row) {
    InstitutionChnlPymMthdAccWebDTO dto = new InstitutionChnlPymMthdAccWebDTO();
    
    // institution_chnnl_pym_mthd_acc verileri
    dto.setId(((BigDecimal) row[0]).longValue());
    dto.setInstitutionChannelPymMethodId(((BigDecimal) row[1]).longValue());
    dto.setCollectionAccountNo((String) row[2]);
    dto.setInstitutionAccountNo((String) row[3]);
    dto.setCurrency((String) row[4]);
    dto.setExpenseType(EnumExpenseType.valueOf((String) row[5]));
    dto.setExpenseAccountNo((String) row[6]);
    dto.setIsActive(((BigDecimal) row[7]).intValue() == 1);
    
    // institution verileri (row[8] ve row[9])
    dto.setInstitutionId(((BigDecimal) row[8]).longValue());  // institutionId
    dto.setInstitutionName((String) row[9]);  // institutionName

    return dto;
}
