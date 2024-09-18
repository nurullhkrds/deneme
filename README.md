@Entity
@Getter
@Setter
@Audited
public class InstitutionFeature extends UpdatableBaseEntity {
	
	@Id
	@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "INSTITUTION_FEATURE_GENERATOR")
	@SequenceGenerator(name = "INSTITUTION_FEATURE_GENERATOR", sequenceName = "SEQ_INSTITUTION_FEATURE", allocationSize = 1)
	@Column(nullable = false, precision = 16, scale = 0)
	private Long id;
	
	@ManyToOne(optional = false, fetch = FetchType.EAGER)
	@JoinColumn(name = "INSTITUTION_ID", referencedColumnName = "ID")
	private Institution institution;
	
	@ManyToOne(optional = false, fetch = FetchType.EAGER)
	@JoinColumn(name = "FEATURE_CODE", referencedColumnName = "CODE")
	@Audited(targetAuditMode = RelationTargetAuditMode.NOT_AUDITED)
	private Feature feature;
	
	@Column(length = 250)
	private String featureValue;
	
	@Column(nullable = false)
	private Boolean isActive;

}



@Getter
@Setter
public class CreateInstitutionFeatureRequest extends BaseCreateWebRequest {

    @NotNull
    @Schema(description = "ID of the institution", example = "1", required = true)
    private Long institutionId;

    @NotNull
    @Schema(description = "Code of the feature", example = "FEATURE_001", required = true)
    private String featureCode;

    @Size(max = 250)
    @Schema(description = "Value of the feature", example = "Some feature value")
    private String featureValue;
    @NotNull
    @Schema(description = "Is the feature active", example = "true", required = true)
    private Boolean isActive;
}




@Entity
@Getter
@Setter
public class Feature {
	
	/**@Convert(converter = EnumFeatureCodeConverter.class)*/
	@Id
	@Enumerated(EnumType.STRING)
	private EnumFeatureCode code;
	
	@Column(nullable = false, length = 100)
	private String name;
	
	@Column(nullable = false, length = 500)
	private String explanation;

/**	@OneToMany(mappedBy= "feature", fetch = FetchType.LAZY,cascade = CascadeType.ALL )
	private List<InstitutionFeature> institutionFeatureList;*/
	
}


@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
public class FeatureDTO  {
	private EnumFeatureCode code;
	private String name;
	private String explanation;
/**	private List<InstitutionFeatureDTO> institutionFeatureList;*/
}


@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
public class InstitutionFeatureDTO extends UpdatableBaseDTO {
	private Long id;
	private InstitutionDTO institution;
	private FeatureDTO feature;
	private String featureValue;
	private Boolean isActive;

}


@Mapper(componentModel = "spring")
public interface InstitutionFeatureMapper {
    InstitutionFeatureMapper INSTANCE = Mappers.getMapper(InstitutionFeatureMapper.class);
    @Mapping(source = "createUser", target = "createdBy")
    @Mapping(source = "institutionId", target = "institution.id")
    InstitutionFeature toInstitutionFeature(CreateInstitutionFeatureRequest request);
}
