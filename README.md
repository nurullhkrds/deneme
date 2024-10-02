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

    // Dışarıdan erişim için bir metot
    List<InstitutionChnlPymMthdAccWebDTO> toWebDTOList(List<Object[]> rows);
    
    default List<InstitutionChnlPymMthdAccWebDTO> toWebDTOList(List<Object[]> rows) {
        return rows.stream()
                   .map(this::fromObjectArray)
                   .collect(Collectors.toList());
    }
