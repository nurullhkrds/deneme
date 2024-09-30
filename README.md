public class InstitutionChannelPymMethod extends UpdatableBaseEntity {
	
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "INSTITUTION_CHANNEL_PYM_METHOD_GENERATOR")
    @SequenceGenerator(name = "INSTITUTION_CHANNEL_PYM_METHOD_GENERATOR", sequenceName = "SEQ_INST_CHANNEL_PYM_METHOD", allocationSize = 1)
	@Column(nullable = false, precision = 16, scale = 0)
	private Long id;
    
	@ManyToOne(fetch = FetchType.EAGER, optional = false)
	@JoinColumn(name = "INSTITUTION_CHANNEL_ID", referencedColumnName = "ID")
	private InstitutionChannel institutionChannel;
	
	@ManyToOne(fetch = FetchType.EAGER, optional = false)
	@JoinColumn(name = "PAYMENT_METHOD", referencedColumnName = "CODE")
	@Audited(targetAuditMode = RelationTargetAuditMode.NOT_AUDITED)
	private PaymentMethod paymentMethod;
	
	@Column(nullable = false, length = 50)
	private String accountingTemplateCode;
	
	@Column(precision = 3, scale = 0)
	private Integer blockDayCount;
	
	@Column(name = "BLOCK_DAY_TYPE")
	@Convert(converter = EnumBlockDayTypeConverter.class)
	private EnumBlockDayType blockDayType;
	
	/**@Convert(converter = EnumBlockDayStrategyCodeConverter.class)*/
	@Enumerated(EnumType.STRING)
	private EnumBlockDayStrategyCode blockDayStrategyCode;
	
	@Column(precision = 18, scale = 9)
	private BigDecimal profitShareRate;
	
	@Column(nullable = false)
	private Boolean isActive;	



@Getter
@Setter
public class CreateInstitutionChnlPymMethodRequest extends BaseCreateWebRequest {


    @NotNull
    @Schema(description = "ID of the institution_channel ", example = "52145", required = true)
    private Long institutionChannelId;

    @NotNull
    @Schema(description = "Enum method of the paymentMethod", example = "PREPAIDCARD", required = true)
    private EnumPaymentMethod paymentMethod;


    @NotNull
    @Schema(description = "Is the feature active", example = "true", required = true)
    @Size(max = 50)
    private String accountingTemplateCode;


    @NotNull
    @Schema(description = "Is the feature active", example = "true", required = true)
    private Integer blockDayCount;

    @NotNull
    @Schema(description = "Is the feature active", example = "W", required = true)
    private EnumBlockDayType blockDayType;


    @NotNull
    @Schema(description = "Is the feature active", example = "CHANNEL", required = true)
    private EnumBlockDayStrategyCode blockDayStrategyCode;




}
