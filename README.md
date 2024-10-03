@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
public class InstitutionWebDTO extends BaseWebDTO {

    private String institutionCode;
    private String institutionName;
    private Long   institutionId;
}

@Getter
@Setter
@EqualsAndHashCode
public class BaseWebDTO implements Serializable {

    private LocalDateTime createDate;

    private String createdBy;

    private LocalDateTime updateDate;

    private String updatedBy;
}

    @Mapping(source = "id", target = "institutionId")
    @Mapping(source = "name", target = "institutionName")
    @Mapping(source = "institutionCode", target = "institutionCode")
    InstitutionWebDTO toInstitutionWebDTO(InstitutionDTO dto);





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



public abstract class UpdatableBaseDTO extends CreatableBaseDTO {
    private Long version;
    private LocalDateTime updateDate;
    private String updatedBy;



public abstract class CreatableBaseDTO extends BaseDTO {
    private LocalDateTime createDate;
    private String createdBy;
