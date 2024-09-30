public class InstitutionPymMethod extends UpdatableBaseEntity {

	@Id
	@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "INSTITUTION_PYM_METHOD_GENERATOR")
	@SequenceGenerator(name = "INSTITUTION_PYM_METHOD_GENERATOR", sequenceName = "SEQ_INSTITUTION_PYM_METHOD", allocationSize = 1)
	@Column(nullable = false, precision = 16, scale = 0)
	private Long id;

	@ManyToOne(optional = false, fetch = FetchType.EAGER)
	@JoinColumn(name = "INSTITUTION_ID", referencedColumnName = "ID")
	private Institution institution;

	@ManyToOne(optional = false, fetch = FetchType.EAGER)
	@JoinColumn(name = "PAYMENT_METHOD", referencedColumnName = "CODE")
	@Audited(targetAuditMode = RelationTargetAuditMode.NOT_AUDITED)
	private PaymentMethod paymentMethod;

	@Column(length = 30)
	private String expenseCode;

	@Column(nullable = false)
	private Boolean isActive;

}
