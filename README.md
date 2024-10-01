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
	}
@AllArgsConstructor
@JsonAdapter(EnumBlockDayTypeConverter.class)
@ToString
public enum EnumBlockDayType {
	
	CALENDAR_DAY("C"),
	WORKING_DAY("W");

	
	@Getter
	@JsonValue
	private String value;
	
	
	private static final Map<String, EnumBlockDayType> paramaters;

	static {
		paramaters = new LinkedHashMap<>();

		for (EnumBlockDayType each : EnumBlockDayType.values()) {
			paramaters.put(each.value, each);
		}

	}	

	@JsonCreator
	public static EnumBlockDayType parse(String value) {
		return paramaters.get(value);
	}
}
@Converter(autoApply = true)
public class EnumBlockDayTypeConverter implements AttributeConverter <EnumBlockDayType,String> {

	@Override
	public String convertToDatabaseColumn(EnumBlockDayType attribute) {
		return (attribute==null) ? null : attribute.getValue();
	}

	@Override
	public EnumBlockDayType convertToEntityAttribute(String dbData) {
		return (dbData == null) ? null : EnumBlockDayType.parse(dbData);
	}

}
@Getter
@Setter
public class CreateInstitutionChnlPymMethodRequest extends BaseCreateWebRequest {

    @NotNull
    @Schema(description = "ID of the institution channel", example = "52145", required = true)
    private Long institutionChannelId;

    @NotNull
    @Schema(description = "Payment method code", example = "PREPAIDCARD", required = true)
    private EnumPaymentMethod paymentMethod;

    @NotNull
    @Size(max = 50)
    @Schema(description = "Accounting template code", example = "ACC_TEMPLATE_001", required = true)
    private String accountingTemplateCode;

    @NotNull
    @Schema(description = "Number of block days", example = "10", required = true)
    private Integer blockDayCount;

    @NotNull
    @Schema(description = "Block day type", example = "W", required = true)
    private EnumBlockDayType blockDayType;

    @NotNull
    @Schema(description = "Block day strategy code", example = "CHANNEL", required = true)
    private EnumBlockDayStrategyCode blockDayStrategyCode;

    @Schema(description = "Profit share rate", example = "0.05", required = true)
    private BigDecimal profitShareRate;

    @NotNull
    @Schema(description = "Is the feature active", example = "true", required = true)
    private Boolean isActive;
}
"Invalid request: JSON parse error: Input mismatch reading Enum `com.ykb.payments.bill.transaction.institution.enums.EnumBlockDayType`: properties-based `@JsonCreator` ([method com.ykb.payments.bill.transaction.institution.enums.EnumBlockDayType#parse(java.lang.String)]) expects JSON Object (JsonToken.START_OBJECT), got JsonToken.VALUE_STRING; nested exception is com.fasterxml.jackson.databind.exc.MismatchedInputException: Input mismatch reading Enum `com.ykb.payments.bill.transaction.institution.enums.EnumBlockDayType`: properties-based `@JsonCreator` ([method com.ykb.payments.bill.transaction.institution.enums.EnumBlockDayType#parse(java.lang.String)]) expects JSON Object (JsonToken.START_OBJECT), got JsonToken.VALUE_STRING\n at [Source: (org.springframework.util.StreamUtils$NonClosingInputStream); line: 7, column: 19] (through reference chain: com.ykb.payments.bill.transaction.institution.admin.web.request.create.CreateInstitutionChnlPymMethodRequest[\"blockDayType\"])"
