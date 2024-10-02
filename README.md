@Mapping(source = "id", target = "id")
    @Mapping(source = "instChannelPymMethod.id", target = "institutionChannelPymMethodId")
    @Mapping(source = "collectionAccountNo", target = "collectionAccountNo")
    @Mapping(source = "institutionAccountNo", target = "institutionAccountNo")
    @Mapping(source = "currency", target = "currency")
    @Mapping(source = "expenseType", target = "expenseType")
    @Mapping(source = "expenseAccountNo", target = "expenseAccountNo")
    @Mapping(source = "isActive", target = "isActive")
    InstitutionChnlPymMthdAccWebDTO toWebDTO(InstitutionChnnlPymMthdAcc acc);
