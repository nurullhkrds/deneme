@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
@EqualsAndHashCode
public class PaidBillResponseWebDTO  implements Serializable {

    /**
	 * 
	 */
	private static final long serialVersionUID = 6121315269201935678L;

	private String id;

    private String productCode;

    private String institutionCode;

    private String debtTypeId;

    private String subscriberNo;

    private String subscriberName;

    private String billDueDate;

    private String billNo;

    private String billTerm;

    private BigDecimal billAmount;

    private BigDecimal billRecalculatedAmount;

    private String currency;

    private String accountNumber;

    private String cardNumber;

    private Long contractNumber;
    
    private String explanation;

    private String additionalInfo1;

    private String additionalInfo2;

    private String additionalInfo3;

    private String additionalInfo4;

    private String additionalInfo5;

    private String additionalInfo6;

    private String additionalInfo7;

    private String additionalInfo8;

    private String additionalInfo9;
}
