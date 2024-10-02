   [
            350859,
            "50623427",
            "49453771",
            "YTL",
            "CUST",
            null,
            1,
            300891
        ],



@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
public class InstitutionChnnlPymMthdAccDTO extends UpdatableBaseDTO {

	private Long id;
	private InstitutionChannelPymMethodDTO institutionChannelPymMethod;
	private String collectionAccountNo;
	private String institutionAccountNo;
	private String currency;
	private EnumExpenseType expenseType;
	private String expenseAccountNo;
	private Boolean isActive;

}

@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
public class InstitutionChannelPymMethodDTO extends UpdatableBaseDTO {

	private Long id;
	private InstitutionChannelDTO institutionChannel;
	private PaymentMethodDTO paymentMethod;
	private String accountingTemplateCode;
	private Integer blockDayCount;
	private EnumBlockDayType blockDayType;
	private EnumBlockDayStrategyCode blockDayStrategyCode;
	private BigDecimal profitShareRate;
	private Boolean isActive;


@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
public class InstitutionChannelDTO extends UpdatableBaseDTO {

	private Long id;	
	private InstitutionDebtTypeDTO institutionDebtType;
	private ChannelDTO channel;
	private Boolean isNewBillNeeded;
	private Boolean isPartialPaymentAllowed;
	private Boolean isOverPaymentAllowed;
	private LocalTime workingStartTime;
	private LocalTime workingFinishTime;
	private Boolean isActive;

}
@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
public class InstitutionDebtTypeDTO extends UpdatableBaseDTO implements Serializable {
	
	private Long id;
	private InstitutionDTO institution;
	private String debtType;
	private String explanation;
	private Boolean isActive;


}
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



  @Mapping(target = "institutionId", source = "institutionChannelPymMethod.institutionChannel.institutionDebtType.institution.id")
    @Mapping(target = "institutionName", source = "institutionChannelPymMethod.institutionChannel.institutionDebtType.institution.name")
    @Mapping(target = "institutionChannelPymMethodId", source = "institutionChannelPymMethod.id")
    InstitutionChnlPymMthdAccWebDTO toWebDTO (InstitutionChnnlPymMthdAccDTO dto);



@Getter
@Setter
public class InstitutionChnlPymMthdAccWebDTO {

    private Long id;
    private String institutionId;
    private String institutionName;
    private Long institutionChannelPymMethodId;
    private String collectionAccountNo;
    private String institutionAccountNo;
    private String currency;
    private EnumExpenseType expenseType;
    private String expenseAccountNo;
    private Boolean isActive;

}

