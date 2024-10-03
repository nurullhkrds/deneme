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

    // DATE'den LocalDateTime'a dönüştürme
    dto.setCreateDate(((Date) row[10]).toInstant()
                     .atZone(ZoneId.systemDefault())
                     .toLocalDateTime());

    dto.setUpdatedBy((String) row[11]);

    // DATE'den LocalDateTime'a dönüştürme
    dto.setUpdateDate(((Date) row[12]).toInstant()
                     .atZone(ZoneId.systemDefault())
                     .toLocalDateTime());

    dto.setInstitutionId(((BigDecimal) row[13]).longValue());
    dto.setInstitutionName((String) row[14]);

    return dto;
}
