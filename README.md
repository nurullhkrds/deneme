@Entity
@Getter
@Setter
@Audited

public class InstitutionDebtType extends UpdatableBaseEntity {
	
	@Id
	@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "INSTITUTION_DEBT_TYPE_GENERATOR")
	@SequenceGenerator(name = "INSTITUTION_DEBT_TYPE_GENERATOR", sequenceName = "SEQ_INSTITUTION_DEBT_TYPE", allocationSize = 1)
	@Column(nullable = false, precision = 16, scale = 0)
	private Long id;
	
	@ManyToOne(optional = false, fetch = FetchType.EAGER)
	@JoinColumn(name = "INSTITUTION_ID", referencedColumnName = "ID")
	private Institution institution;
		
	@Column(nullable = false, length = 30)
	private String debtType;
	
	@Column(nullable = false, length = 500)
	private String explanation;
	
	@Column(nullable = false)
	private Boolean isActive;
	


}
