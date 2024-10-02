   @Mappings({
        @Mapping(target = "id", source = "0"),
        @Mapping(target = "institutionChannelPymMethodId", source = "1"),
        @Mapping(target = "collectionAccountNo", source = "2"),
        @Mapping(target = "institutionAccountNo", source = "3"),
        @Mapping(target = "currency", source = "4"),
        @Mapping(target = "expenseType", source = "5"),
        @Mapping(target = "expenseAccountNo", source = "6"),
        @Mapping(target = "isActive", source = "7")
    })
    InstitutionChnlPymMthdAccWebDTO toWebDTO(Object[] row);

    // Dönüşüm metodu için ek bir yardım metodu oluştur
    default InstitutionChnlPymMthdAccWebDTO fromObjectArray(Object[] row) {
        InstitutionChnlPymMthdAccWebDTO dto = new InstitutionChnlPymMthdAccWebDTO();
        dto.setId((Long) row[0]);
        dto.setInstitutionChannelPymMethodId((Long) row[1]);
        dto.setCollectionAccountNo((String) row[2]);
        dto.setInstitutionAccountNo((String) row[3]);
        dto.setCurrency((String) row[4]);
        dto.setExpenseType(EnumExpenseType.valueOf((String) row[5]));
        dto.setExpenseAccountNo((String) row[6]);
        dto.setIsActive(row[7] != null && (Integer) row[7] == 1);
        return dto;
    }
