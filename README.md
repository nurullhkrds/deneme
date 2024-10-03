@Entity
@Getter
@Setter
@Audited
@AuditTable(value="INST_CHNNL_PYM_MTHD_PSC_AUD")
public class InstitutionChnnlPymMthdPsc extends UpdatableBaseEntity {
	
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "INSTITUTION_CHNNL_PYM_MTHD_PSC_GENERATOR")
    @SequenceGenerator(name = "INSTITUTION_CHNNL_PYM_MTHD_PSC_GENERATOR", sequenceName = "SEQ_INST_CHNNL_PYM_MTHD_PSC", allocationSize = 1)
	@Column(nullable= false, name = "ID", precision = 16, scale = 0)
	private Long id;
    
    @OneToOne(optional = false, fetch = FetchType.EAGER)
	@JoinColumn(name = "INST_CHANNEL_PYM_METHOD_ID", referencedColumnName = "ID")
	private InstitutionChannelPymMethod institutionChannelPymMethod;
	
	@Column(nullable= false, precision = 2, scale = 0)
	private Integer mondayBlockDayCount;
	
	@Column(nullable= false, precision = 2, scale = 0)
	private Integer tuesdayBlockDayCount;
	
	@Column(nullable= false, precision = 2, scale = 0)
	private Integer wednesdayBlockDayCount;
	
	@Column(nullable= false, precision = 2, scale = 0)
	private Integer thursdayBlockDayCount;
	
	@Column(nullable= false, precision = 2, scale = 0)
	private Integer fridayBlockDayCount;
	
	@Column(nullable= false, precision = 2, scale = 0)
	private Integer saturdayBlockDayCount;
	
	@Column(nullable= false, precision = 2, scale = 0)
	private Integer sundayBlockDayCount;
	
	
}
