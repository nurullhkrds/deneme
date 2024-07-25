@Service
@RequiredArgsConstructor
public class PaymentCommissionServiceImpl implements PaymentCommissionService{
	
	    private final InstitutionPaymentMethodService instPaymentMethodService;
	    
	    private final InstitutionDebtTypeService institutionDebtTypeService;	  	  

	    private final CommissionService commissionService;	    
	    
	    private final ProvisionService provisionService;

	    public static final String YTL_CURRENCY = "YTL";
	    public static final String TL_CURRENCY = "TL";
	    public static final String TRY_CURRENCY = "TRY";
	    public static final String USER_CODE_INT001 = "INT001";
	    public static final String BRANCH_CODE_925 = "925";
	    public static final String CREDIT_CARD = "CREDIT_CARD";
	    

		@Override
		public ResponseCommissionInformation performCommission(CommissionServiceRequestDTO commissionServiceRequestDTO)
				throws MicroException {

			try {

				ProvisionDTO provisionRecord = provisionService
						.getProvisionRecord(commissionServiceRequestDTO.getBillProvisionId());

				InstitutionDebtTypeDTO debtType = institutionDebtTypeService
						.getDebtType(provisionRecord.getInstitutionDebtTypeId());

				InstitutionPymMethodWebDTO institutionPymMethod = instPaymentMethodService.getInstitutionExpenseCode(
						debtType.getInstitution().getProduct().getCode(),
						debtType.getInstitution().getInstitutionCode(),
						commissionServiceRequestDTO.getPaymentMethod(),
						provisionRecord.getInstitutionDebtTypeId(), commissionServiceRequestDTO.getChannelCode());

				if(institutionPymMethod == null || StringUtils.isEmpty(institutionPymMethod.getExpenseCode())){
					return null;
				}

				commissionServiceRequestDTO.setExpenseCode(institutionPymMethod.getExpenseCode());

				if (institutionPymMethod.getExpenseType().equals(EnumExpenseType.FROM_CUSTOMER.getValue())) {
					commissionServiceRequestDTO.setAccountNo(institutionPymMethod.getExpenseAccountNo());
				}

				RequestCommissionInformation requestCommissionInformation = prepareRequestCommissionInformation(
						commissionServiceRequestDTO);

				requestCommissionInformation.setClientNo(provisionRecord.getCustomerNo());

				ResponseCommissionInformation responseCommissionInformation = commissionService
						.inquireCommission(requestCommissionInformation);

				ObjectMapper objectMapper = new ObjectMapper();

				String commissionData = objectMapper.writeValueAsString(responseCommissionInformation);

				provisionService.updateCommissionData(commissionData, commissionServiceRequestDTO.getBillProvisionId());

				return responseCommissionInformation;

			} catch (Exception ex) {
				throw new BillException(BillTransactionConstant.APP_NAME, EnumBillResult.BILL_EXPENSE_INQUIRY_ERROR);
			}

		}

		private RequestCommissionInformation prepareRequestCommissionInformation(
				CommissionServiceRequestDTO commissionServiceRequestDTO) throws MicroException {
			
			BillValidationUtil.validateCondition(
					Arrays.asList(EnumPaymentMethod.ACCOUNT.getValue(), EnumPaymentMethod.CARD.getValue(),EnumPaymentMethod.PREPAIDCARD.getValue(),EnumPaymentMethod.CASH.getValue())
							.contains(commissionServiceRequestDTO.getPaymentMethod()),
					EnumBillResult.PAYMENT_METHOD_NOT_SUPPORTED, BillTransactionConstant.APP_NAME);

			if (EnumPaymentMethod.ACCOUNT.getValue().equals(commissionServiceRequestDTO.getPaymentMethod())) {
				return prepareCommissionInformationReqForAccount(commissionServiceRequestDTO);
			}
			if(EnumPaymentMethod.CARD.getValue().equals(commissionServiceRequestDTO.getPaymentMethod()) || EnumPaymentMethod.PREPAIDCARD.getValue().equals(commissionServiceRequestDTO.getPaymentMethod())) {
				return prepareCommissionInformationReqForCreditCard(commissionServiceRequestDTO);
			} else {
	            //TODO: CASH;
				return null;
	        }
		}

	    private RequestCommissionInformation prepareCommissionInformationReqForAccount(CommissionServiceRequestDTO commissionServiceRequestDTO) {

	        RequestCommissionInformation requestCommissionInformation = new RequestCommissionInformation();

	        requestCommissionInformation.setAccountingType(EnumPaymentMethod.ACCOUNT.getValue());
	        requestCommissionInformation.setAccountNo(commissionServiceRequestDTO.getAccountNo());
	        requestCommissionInformation.setAccountCurrency(currencyConverter(commissionServiceRequestDTO.getPaymentCurrency()));
	        requestCommissionInformation.setTransactionAmount(commissionServiceRequestDTO.getPaymentAmount());
	        requestCommissionInformation.setTransactionCurrency(currencyConverter(commissionServiceRequestDTO.getPaymentCurrency()));

	        requestCommissionInformation.setChannelCode(commissionServiceRequestDTO.getChannelCode());
	        requestCommissionInformation.setBranch(commissionServiceRequestDTO.getAccountBranchCode());
	        requestCommissionInformation.setUserCode(USER_CODE_INT001);

	        List<CommissionInputDetailApiDTO> commissionInputDetailApiDTOList = new ArrayList<>();

	        CommissionInputDetailApiDTO commissionInputDetailApiDTO = new CommissionInputDetailApiDTO();
	        commissionInputDetailApiDTO.setCommissionAccountNo(commissionServiceRequestDTO.getAccountNo());
	        commissionInputDetailApiDTO.setOperationCode(commissionServiceRequestDTO.getExpenseCode());
	        commissionInputDetailApiDTO.setCommissionCurrency(currencyConverter(commissionServiceRequestDTO.getPaymentCurrency()));

	        commissionInputDetailApiDTOList.add(commissionInputDetailApiDTO);

	        requestCommissionInformation.setCommissionInputDetailApiDTOList(commissionInputDetailApiDTOList);

	        return requestCommissionInformation;

	    }

	    private RequestCommissionInformation prepareCommissionInformationReqForCreditCard(CommissionServiceRequestDTO commissionServiceRequestDTO) {

	        RequestCommissionInformation requestCommissionInformation = new RequestCommissionInformation();
	        
	        requestCommissionInformation.setAccountingType(CREDIT_CARD);
	        requestCommissionInformation.setAccountNo(null);
	        requestCommissionInformation.setAccountCurrency(null);
	        requestCommissionInformation.setTransactionAmount(commissionServiceRequestDTO.getPaymentAmount());
	        requestCommissionInformation.setTransactionCurrency(currencyConverter(commissionServiceRequestDTO.getPaymentCurrency()));

	        requestCommissionInformation.setChannelCode(commissionServiceRequestDTO.getChannelCode());
	        requestCommissionInformation.setBranch(BRANCH_CODE_925);
	        requestCommissionInformation.setUserCode(USER_CODE_INT001);

	        List<CommissionInputDetailApiDTO> commissionInputDetailApiDTOList = new ArrayList<>();

	        CommissionInputDetailApiDTO commissionInputDetailApiDTO = new CommissionInputDetailApiDTO();
	        commissionInputDetailApiDTO.setOperationCode(commissionServiceRequestDTO.getExpenseCode());
	        commissionInputDetailApiDTO.setCommissionCurrency(currencyConverter(commissionServiceRequestDTO.getPaymentCurrency()));

	        commissionInputDetailApiDTOList.add(commissionInputDetailApiDTO);

	        requestCommissionInformation.setCommissionInputDetailApiDTOList(commissionInputDetailApiDTOList);

	        return requestCommissionInformation;

	    }
	    
	    public static String currencyConverter(String currency) {
	        if (currency == null || currency.equals(TL_CURRENCY) || currency.equals(TRY_CURRENCY)) {
	            return YTL_CURRENCY;
	        }
	        return currency;
	    }


}
