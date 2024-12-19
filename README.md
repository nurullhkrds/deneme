  default InstitutionChnlProcessWebDTO objectArrayToWebDTO(Object[] row) {
        InstitutionChnlProcessWebDTO dto = new InstitutionChnlProcessWebDTO();

        dto.setId(((BigDecimal) row[0]).longValue());
        dto.setInstitutionChannelId(((BigDecimal) row[1]).longValue());
        dto.setInstitutionProcessId(((BigDecimal) row[2]).longValue());
        dto.setWorkingStartTime(LocalTime.parse((String) row[3]));
        dto.setWorkingFinishTime(LocalTime.parse((String) row[4]));
        dto.setIsActive(((BigDecimal) row[5]).intValue() == 1);
        dto.setInstitutionId(((BigDecimal) row[6]).longValue());
        dto.setInstitutionName((String) row[7]);

        dto.setCreatedBy((String) row[8]);

        dto.setCreateDate(((Date) row[9]).toInstant()
                .atZone(ZoneId.systemDefault())
                .toLocalDateTime());

        dto.setUpdatedBy((String) row[10]);

        if (row[11] != null)
            dto.setUpdateDate(((Date) row[11]).toInstant()
                    .atZone(ZoneId.systemDefault())
                    .toLocalDateTime());


        return dto;
    }

dönüşümüm mapper bu şekildedir. 



@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
public class InstitutionChannelProcessDTO extends UpdatableBaseDTO {
	
	private Long id;
	private InstitutionChannelDTO institutionChannel;	
	private InstitutionProcessDTO institutionProcess;
	private LocalTime workingStartTime;
	private LocalTime workingFinishTime;
	private Boolean isActive;

}
