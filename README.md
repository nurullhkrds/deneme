@Query(value = "SELECT id, " + 
               "inst_channel_pym_method_id, " + 
               "collection_account_no, " + 
               "institution_account_no, " + 
               "currency, " + 
               "expense_type, " + 
               "expense_account_no, " + 
               "is_active " + 
               "FROM institution_chnnl_pym_mthd_acc", 
       nativeQuery = true)
List<InstitutionChnlPymMthdAccWebDTO> findAllInstitutionChnlPymMthdAccs();
