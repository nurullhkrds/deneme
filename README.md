@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
@EqualsAndHashCode
public class InstitutionCityWebDTO {

    private Long id;
    private String institutionId;
    private String institutionName;
    private String city;
    private Boolean isActive;
}


@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
public class InstitutionCityDTO extends UpdatableBaseDTO {

	private Long id;
	private InstitutionDTO institution;
	private CityDTO city;
	private Boolean isActive;

}

@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
public class InstitutionDTO extends UpdatableBaseDTO implements Serializable {

	private Long id;
	private ProductDTO product;
	private String institutionCode;
	private Long customerNo;
	private String name;
	private String explanation;
	private OwnerDepartmentDTO ownerDepartment;
	private LocalDate protocolStartDate;
	private LocalDate protocolEndDate;
	private Boolean isReverseAllowed;
	private Boolean isOrderAllowed;
	private Boolean hasDebtType;
	private String iconText;
	private Boolean isActive;

}
@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
public class CityDTO {

	private String code;
	private String name;

}

 List<InstitutionCityWebDTO> toWebDTOList(List<InstitutionCityDTO> dtoList);

    InstitutionCityWebDTO toWebDTO(InstitutionCityDTO dto);
