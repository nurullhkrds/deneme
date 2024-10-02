@Mapping(target = "id", source = "row[0]")
    @Mapping(target = "collectionAccountNo", source = "row[1]")
    @Mapping(target = "institutionAccountNo", source = "row[2]")
    @Mapping(target = "currency", source = "row[3]")
    @Mapping(target = "expenseType", source = "row[4]")
    @Mapping(target = "expenseAccountNo", source = "row[5]")
    @Mapping(target = "isActive", expression = "java(row[6] != null && (Integer) row[6] == 1)")
    @Mapping(target = "institutionChannelPymMethod.id", source = "row[7]")
    InstitutionChnnlPymMthdAccDTO toDTO(Object[] row);
