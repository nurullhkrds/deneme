@Getter
@Setter
public class PaymentDTO extends BaseTransactionalDTO {
	
	private Long id;
	private Long institutionId;
	private Long institutionDebtTypeId;
	private String subscriberNo;
	private LocalDate billDueDate;
	private String billNo;
	private Integer transactionOrderNo;
	private BigDecimal amount;
	private BigDecimal paymentAmount;
	private EnumCurrencyCode currency;
	private EnumBillStatu status;
	private String subscriberName;
	private Long provisionId;
	private Long customerNo;
	private Long identityNo;
	private String taxId;
	private EnumPaymentMethod paymentMethod;
	private LocalDate paymentDate;
	private LocalTime paymentTime;
	private Long contractNo;
	private BigDecimal expenseAmount;
	private EnumExpenseType expenseType;
	private String expenseAccountNo;
	private String accountNo;
	private String creditCardNo;	
	private Boolean isBusinessCard;
	private String provisionRequestId;
	private Long oceanTransactionId;
	private EnumAccountingSource accountingSources;
	private LocalDate availableDate;
	private String institutionAccountNo;
	private Long commissionInquiryId;
	private LocalDate billLoadDate;
	private String billTerm;
	private LocalDate billIssueDate;
	private String explanation;
	private String queryStan;
	private String paymentStan;
	private String cancelStan;
	private String institutionQueryStan;
	private String institutionPaymentStan;
	private String institutionCancelStan;
	private String additionalInfo1;
	private String additionalInfo2;
	private String additionalInfo3;
	private String additionalInfo4;
	private String additionalInfo5;
}
