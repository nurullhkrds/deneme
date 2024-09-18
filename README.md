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


Optional<InstitutionFeature> existingInstitutionFeature = institutionFeatureRepository
				.findByInstitutionIdAndFeatureCode(request.getInstitutionId(), request.getFeatureCode());
