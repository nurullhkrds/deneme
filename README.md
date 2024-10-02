 default InstitutionChnlPymMthdAccWebDTO fromObjectArray(Object[] row) {
        InstitutionChnlPymMthdAccWebDTO dto = new InstitutionChnlPymMthdAccWebDTO();
        
        // BigDecimal'dan Long'a dönüştürme
        dto.setId(((BigDecimal) row[0]).longValue()); // ID
        dto.setInstitutionChannelPymMethodId(((BigDecimal) row[1]).longValue()); // INST_CHANNEL_PYM_METHOD_ID
        
        dto.setCollectionAccountNo((String) row[2]);
        dto.setInstitutionAccountNo((String) row[3]);
        dto.setCurrency((String) row[4]);
        dto.setExpenseType(EnumExpenseType.valueOf((String) row[5])); // Enum dönüşümü
        dto.setExpenseAccountNo((String) row[6]);
        dto.setIsActive(row[7] != null && (Integer) row[7] == 1); // Boolean dönüşümü
        
        return dto;
    }
