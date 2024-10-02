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
    List<Object[]> findInstitutionChnlPymMthdAccsAsObjectArray();

 @Mapping(target = "id", source = "0")
    @Mapping(target = "institutionChannelPymMethodId", source = "1")
    @Mapping(target = "collectionAccountNo", source = "2")
    @Mapping(target = "institutionAccountNo", source = "3")
    @Mapping(target = "currency", source = "4")
    @Mapping(target = "expenseType", source = "5")
    @Mapping(target = "expenseAccountNo", source = "6")
    @Mapping(target = "isActive", source = "7")
    InstitutionChnlPymMthdAccWebDTO toWebDTO(Object[] row);



    List<Object[]> results = repository.findInstitutionChnlPymMthdAccsAsObjectArray();
        List<InstitutionChnlPymMthdAccWebDTO> dtos = new ArrayList<>();

        for (Object[] row : results) {
            InstitutionChnlPymMthdAccWebDTO dto = mapper.toWebDTO(row);
            dtos.add(dto);
        }

        return dtos;
