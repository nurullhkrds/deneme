public class  GetBillPaymentExpenseRequestDTO extends BaseWebRequest {

	private String productCode;
	private String institutionCode;
	private String billProvisionId;
	private String paymentMethod;
	private String accountNo;
	private String accountBranchCode;
	private String cardNo;
	private BigDecimal paymentAmount;
	private String paymentCurrency;

}

@Getter
@Setter
public class GetBillPaymentExpenseResponseDTO extends BaseWebResponse {


    private BigDecimal commissionAmount;


}
