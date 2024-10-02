@Query(value = "SELECT icpa.id AS acc_id, " + 
               "icpa.collection_account_no AS collection_account_no, " + 
               "icpa.institution_account_no AS institution_account_no, " + 
               "icpa.currency AS currency, " + 
               "icpa.expense_type AS expense_type, " + 
               "icpa.expense_account_no AS expense_account_no, " + 
               "icpa.is_active AS is_active, " + 
               "icpm.id AS pym_method_id " + // Farklı bir alias kullandık
               "FROM institution_chnnl_pym_mthd_acc icpa " +
               "JOIN institution_channel_pym_method icpm ON icpa.inst_channel_pym_method_id = icpm.id", 
       nativeQuery = true)
List<Object[]> findInstitutionChnnlPymMthdAccWithInstitutionChannelPymMethod();
