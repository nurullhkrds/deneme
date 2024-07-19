@Service
@RequiredArgsConstructor
public class AccountingServiceImpl implements AccountingService {

    private final ProvisionFactory  provisionFactory;
    private final ReverseProvisionFactory reverseProvisionFactory;
    private final ReceiptService receiptService;

    @Override
    public CreateAccountingResultDTO doAccounting(CreateAccountingDTO createAccountingDTO) {
        ProvisionService provisionService = provisionFactory.getProvisionService(createAccountingDTO.getPaymentMethodType().getProvisionType());
        prepareCreateAccountingDTO(createAccountingDTO);
        CreateAccountingResultDTO createAccountingResultDTO = provisionService.doAccounting(createAccountingDTO);
        if(createAccountingResultDTO.isSuccess()) {
            prepareCreateAccountingResultDTO(createAccountingResultDTO,createAccountingDTO);
            receiptService.printReceipt(createAccountingDTO, createAccountingResultDTO);
        }
        return createAccountingResultDTO;
    }

	@Override
	public CreateReverseAccountingResultDTO doReverseAccounting(CreateReverseAccountingDTO createReverseAccountingDTO) {
		ReverseProvisionService reverseProvisionService = reverseProvisionFactory.getReverseProvisionService(createReverseAccountingDTO.getPaymentMethodType().getProvisionType());
		return reverseProvisionService.doReverseAccounting(createReverseAccountingDTO);
	}

	private CreateAccountingDTO prepareCreateAccountingDTO(CreateAccountingDTO createAccountingDTO) {
        if(createAccountingDTO.getProvisionDTO().getCommissionData()==null){
            return createAccountingDTO;
        }

        ObjectMapper objectMapper = new ObjectMapper();
        try {
            createAccountingDTO.setResponseCommissionInformation(objectMapper.readValue(createAccountingDTO.getProvisionDTO().getCommissionData(), ResponseCommissionInformation.class));
        } catch (JsonProcessingException e) {
            e.printStackTrace();
        }
        return createAccountingDTO;
    }

    private CreateAccountingResultDTO prepareCreateAccountingResultDTO(CreateAccountingResultDTO createAccountingResultDTO, CreateAccountingDTO createAccountingDTO) {

        BigDecimal totalPaymentAmount = createAccountingDTO.getPaymentAmount();
        if (createAccountingDTO.getResponseCommissionInformation() != null) {
            BigDecimal commissionAmount = createAccountingDTO.getResponseCommissionInformation().getTotalCommissionLocalCurrencyAmount();
            commissionAmount = commissionAmount.add(createAccountingDTO.getResponseCommissionInformation().getTotalCommissionTaxLocalCurrencyAmount());
            createAccountingResultDTO.setCommissionAmount(commissionAmount);
            totalPaymentAmount = totalPaymentAmount.add(commissionAmount);
        } else {
            createAccountingResultDTO.setCommissionAmount(BigDecimal.ZERO);
        }

        createAccountingResultDTO.setTotalPaymentAmount(totalPaymentAmount);
        createAccountingResultDTO.setPendingDetailList(createAccountingResultDTO.getPendingDetailList());
        return createAccountingResultDTO;
    }
}
