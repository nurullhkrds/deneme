    default InstitutionChnlPymMthdAccWebDTO objectArrayToWebDTO(Object[] row) {
        InstitutionChnlPymMthdPscWebDTO dto = new InstitutionChnlPymMthdPscWebDTO();

        dto.setId(((BigDecimal) row[0]).longValue());
        dto.setInstitutionChannelPymMethodId(((BigDecimal) row[1]).longValue());
        dto.setMondayBlockDayCount(((BigDecimal) row[2]).intValue());
        dto.setTuesdayBlockDayCount(((BigDecimal) row[3]).intValue());
        dto.setWednesdayBlockDayCount(((BigDecimal) row[4]).intValue());
        dto.setThursdayBlockDayCount(((BigDecimal) row[5]).intValue());
        dto.setFridayBlockDayCount(((BigDecimal) row[6]).intValue());
        dto.setSaturdayBlockDayCount(((BigDecimal) row[7]).intValue());
        dto.setSundayBlockDayCount(((BigDecimal) row[8]).intValue());
        dto.setCreatedBy((String) row[9]);
        dto.setCreateDate();
        dto.setUpdatedBy((String) row[11]);
        dto.getUpdateDate();
        dto.setInstitutionId(((BigDecimal) row[8]).longValue());
        dto.setInstitutionName((String) row[9]);
        return dto;
    }
