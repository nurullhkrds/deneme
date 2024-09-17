@MappedSuperclass
@Audited
@EntityListeners({CreatableBaseEntityListener.class})
public class CreatableBaseEntity extends BaseEntity {
    private LocalDateTime createDate;
    private @Size(
    max = 10
) String createdBy;

    public CreatableBaseEntity() {
    }

    public LocalDateTime getCreateDate() {
        return this.createDate;
    }

    public String getCreatedBy() {
        return this.createdBy;
    }

    public void setCreateDate(final LocalDateTime createDate) {
        this.createDate = createDate;
    }

    public void setCreatedBy(final String createdBy) {
        this.createdBy = createdBy;
    }
}

@MappedSuperclass
@Audited
@EntityListeners({UpdatableBaseEntityListener.class})
public class UpdatableBaseEntity extends CreatableBaseEntity {
    @Version
    private @NotNull Long version;
    @Column
    private LocalDateTime updateDate;
    @Column(
        length = 10
    )
    private String updatedBy;

    public UpdatableBaseEntity() {
    }

    public Long getVersion() {
        return this.version;
    }

    public LocalDateTime getUpdateDate() {
        return this.updateDate;
    }

    public String getUpdatedBy() {
        return this.updatedBy;
    }

    public void setVersion(final Long version) {
        this.version = version;
    }

    public void setUpdateDate(final LocalDateTime updateDate) {
        this.updateDate = updateDate;
    }

    public void setUpdatedBy(final String updatedBy) {
        this.updatedBy = updatedBy;
    }
}

public class Institution extends UpdatableBaseEntity {
	
	@Id
	@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "INSTITUTION_ID_GENERATOR")
	@SequenceGenerator(name = "INSTITUTION_ID_GENERATOR", sequenceName = "SEQ_INSTITUTION", allocationSize = 1)
	@Column(nullable = false, precision = 16, scale = 0)	
	private Long id;
	
	@ManyToOne(optional = false, fetch = FetchType.EAGER)
	@JoinColumn(name = "PRODUCT_CODE", referencedColumnName = "CODE")
	@Audited(targetAuditMode = RelationTargetAuditMode.NOT_AUDITED)
	private Product product;
	
	@Column(nullable = false, length = 50)
	private String institutionCode;
	
	@Column(nullable = false, precision = 12, scale = 0)
	private Long customerNo;
	
	@Column(nullable = false,length = 100)
	private String name;

	@Column(nullable = false,length = 500)
	private String explanation;
	
	@ManyToOne (optional = false, fetch = FetchType.LAZY)
	@JoinColumn(name = "OWNER_DEPARTMENT_CODE", referencedColumnName = "CODE")
	@Audited(targetAuditMode = RelationTargetAuditMode.NOT_AUDITED)
	private OwnerDepartment ownerDepartment;
	
	@Column
	private LocalDate protocolStartDate;
	
	@Column
	private LocalDate protocolEndDate;
	
	@Column(nullable = false)
	private Boolean isReverseAllowed;
	
	@Column(nullable = false)
	private Boolean isOrderAllowed;
	
	@Column(nullable = false)
	private Boolean hasDebtType;
	
	@Column(length = 1000)
	private String iconText;
	
	@Column(nullable = false)
	private Boolean isActive;
	

}


BURASI NESNENİN DEĞİŞKENLERİ 





@Getter
@Setter
public class BaseCreateWebRequest {

    @NotNull
    @Size(min = 1, max = 10)
    @Schema(description = "The create user of the record", example = "U099999", requiredMode = Schema.RequiredMode.REQUIRED)
    protected String createUser;



}
public class CreateInstitutionRequest extends BaseCreateWebRequest {



}
    @Override
    public DataResult<AdapterInstitutionDTO> createInstitution(CreateInstitutionRequest request) {


    }
