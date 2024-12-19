InstitutionChannelProcessDTO dto = new InstitutionChannelProcessDTO();

        dto.setId(((BigDecimal) row[0]).longValue());

        // Yeni InstitutionChannelDTO oluştur ve id'yi ayarla
        InstitutionChannelDTO institutionChannelDTO = new InstitutionChannelDTO();
        institutionChannelDTO.setId(((BigDecimal) row[1]).longValue());
        dto.setInstitutionChannel(institutionChannelDTO);

        // Yeni InstitutionProcessDTO oluştur ve id'yi ayarla
        InstitutionProcessDTO institutionProcessDTO = new InstitutionProcessDTO();
        institutionProcessDTO.setId(((BigDecimal) row[2]).longValue());
        dto.setInstitutionProcess(institutionProcessDTO);

        dto.setWorkingStartTime(LocalTime.parse((String) row[3]));
        dto.setWorkingFinishTime(LocalTime.parse((String) row[4]));
        dto.setIsActive(((BigDecimal) row[5]).intValue() == 1);

        // createdBy, createDate, updatedBy, updateDate alanlarını ayarla
        dto.setCreatedBy((String) row[8]);
        dto.setCreateDate(((Date) row[9]).toInstant().atZone(ZoneId.systemDefault()).toLocalDateTime());
        dto.setUpdatedBy((String) row[10]);
        if (row[11] != null) {
            dto.setUpdateDate(((Date) row[11]).toInstant().atZone(ZoneId.systemDefault()).toLocalDateTime());
        }

        return dto;
