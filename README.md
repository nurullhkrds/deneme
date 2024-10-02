INST_CHANNEL_PYM_METHOD_ID, CURRENCY


INST_CHANNEL_PYM_METHOD_ID, COLLECTION_ACCOUNT_NO



@Entity
@Getter
@Setter
@Audited
@AuditTable(value="INST_CHNNL_PYM_MTHD_ACC_AUD")
public class InstitutionChnnlPymMthdAcc extends UpdatableBaseEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "INSTITUTION_CHNNL_PYM_MTHD_ACC_GENERATOR")
    @SequenceGenerator(name = "INSTITUTION_CHNNL_PYM_MTHD_ACC_GENERATOR", sequenceName = "SEQ_INST_CHNNL_PYM_MTHD_ACC", allocationSize = 1)
	@Column(nullable= false, precision = 16, scale = 0)
	private Long id; 
    
	@ManyToOne(fetch = FetchType.EAGER, optional = false)
	@JoinColumn(name = "INST_CHANNEL_PYM_METHOD_ID", referencedColumnName = "ID")
	private InstitutionChannelPymMethod institutionChannelPymMethod;
	
	@Column(nullable = false, length = 9)
	private String collectionAccountNo;
	
	@Column(nullable = false, length = 9)
	private String institutionAccountNo;
	
	@Column(nullable = false, name = "CURRENCY", length = 4)
	private String currency;
	
	@Column(name = "EXPENSE_TYPE")
	@Convert(converter = EnumExpenseTypeConverter.class)
	private EnumExpenseType expenseType;
	
	@Column(length = 9)
	private String expenseAccountNo;
	
	@Column(nullable = false)
	private Boolean isActive;
	
}

@Getter
@Setter
public class CreateInstitutionChnlPymMthdAccRequestDTO extends BaseCreateRequestDTO {

    private Long id;

    private Long institutionChannelPymMethodId;


    private String collectionAccountNo;


    private String institutionAccountNo;


    private String currency;


    private EnumExpenseType expenseType;


    private String expenseAccountNo;

    private Boolean isActive;
}


  @Override
    public InstitutionChnnlPymMthdAccDTO createInstitutionChannelPymMethodAcc(CreateInstitutionChnlPymMthdAccRequestDTO requestDTO) throws MicroException {


        return null;
    }
