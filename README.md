@Service
@RequiredArgsConstructor
public class CardReverseProvisionServiceImpl implements ReverseProvisionService{

    private static  final EnumProvisionType provisionType = EnumProvisionType.CARD;
    
    private final ProvisionNextService provisionNextService;

	@Override
	public EnumProvisionType getProvisionType() {
		return provisionType;
	}

	@Override
	public CreateReverseAccountingResultDTO doReverseAccounting(CreateReverseAccountingDTO createReverseAccountingDTO) {
		
		CreateReverseAccountingResultDTO createReverseAccountingResultDTO = new CreateReverseAccountingResultDTO();

		/* Gerçek üye işyeri için iptal muhasebesi yapılmayacak  */
		if(!createReverseAccountingDTO.isDummyMerchant()) {
			createReverseAccountingResultDTO.setSuccess(true);
			return createReverseAccountingResultDTO;
		}
		
		MakeReverseProvisionRequest makeReverseProvisionRequest = prepareReverseProvisionRequest(createReverseAccountingDTO);
		try {
			MakeReverseProvisionResponse makeReverseProvision = provisionNextService.makeReverseProvision(makeReverseProvisionRequest);	// TODO: Servisten donen degerlerin hangisi kullanılacak?

			if(!makeReverseProvision.isSuccess()){
				handleException(makeReverseProvision.getErrorCode(), createReverseAccountingResultDTO);
				createReverseAccountingResultDTO.setSuccess(false);
				return createReverseAccountingResultDTO;
			}
			createReverseAccountingResultDTO.setSuccess(true);
		}catch (Exception e){
			if(e.getCause().getClass().equals(ServiceCallException.class)){
				Long errorCode =((ServiceCallException) e.getCause()).getErrorCode();
				handleException(errorCode, createReverseAccountingResultDTO);
				return createReverseAccountingResultDTO;
			}
			createReverseAccountingResultDTO.setError(EnumBillResult.GENERIC_UNKNOWN_ERROR);
			createReverseAccountingResultDTO.setSuccess(false);
		}

		return createReverseAccountingResultDTO;
	}

	private MakeReverseProvisionRequest prepareReverseProvisionRequest(CreateReverseAccountingDTO createReverseAccountingDTO) {
		MakeReverseProvisionRequest makeReverseProvisionRequest = new MakeReverseProvisionRequest();
		makeReverseProvisionRequest.setTransactionId(createReverseAccountingDTO.getChannelTransactionId());
		makeReverseProvisionRequest.setContractNo(createReverseAccountingDTO.getContractNo());
		makeReverseProvisionRequest.setReverseDescriptionAppendix("İPTAL"); //TODO: Bu alan nasıl doldurulacak?
		return makeReverseProvisionRequest;
	}

	private void handleException(Long errorCode, CreateReverseAccountingResultDTO createReverseAccountingResultDTO){
		EnumAccountProvisionResult result = EnumAccountProvisionResult.parse(errorCode);
		createReverseAccountingResultDTO.setSuccess(false);
		if(result == null){
			createReverseAccountingResultDTO.setError(EnumBillResult.GENERIC_UNKNOWN_ERROR);
			return;
		}
		createReverseAccountingResultDTO.setError(result.getBillCode());
	}
	

}



BU SINIFIN TESTİ BUDUR 
"class CardReverseProvisionServiceImplTest {


    private CardReverseProvisionServiceImpl cardReverseProvisionService;
    private  ProvisionNextService provisionNextService;


    @BeforeEach
    void setUp() {
        provisionNextService = mock(ProvisionNextService.class);
        cardReverseProvisionService=new CardReverseProvisionServiceImpl(provisionNextService);

    }

    @Test
    void shouldReturnProvisionType() {
        EnumProvisionType expectedProvisionType = EnumProvisionType.CARD;
        EnumProvisionType actualProvisionType = cardReverseProvisionService.getProvisionType();
        assertEquals(expectedProvisionType, actualProvisionType);

    }

    @Test
    void shouldReturnIsDummyMerchantFalse(){
        CreateReverseAccountingDTO inputDto=new CreateReverseAccountingDTO();
        inputDto.setDummyMerchant(false);
        CreateReverseAccountingResultDTO actualDto=new CreateReverseAccountingResultDTO();
        actualDto.setSuccess(true);

        CreateReverseAccountingResultDTO resultDTO= cardReverseProvisionService.doReverseAccounting(inputDto);
        assertEquals(resultDTO.isSuccess(),actualDto.isSuccess());


    }




    @Test
    void shouldReturnIsDummyMerchantTrueButMakeReverseProvisionFalse() {
        CreateReverseAccountingDTO inputDto = new CreateReverseAccountingDTO();
        inputDto.setPaymentMethodType(EnumPaymentMethod.CARD);
        inputDto.setContractNo(123L);
        inputDto.setChannelTransactionId("123");
        inputDto.setDummyMerchant(true);

        CreateReverseAccountingResultDTO expectedResult = new CreateReverseAccountingResultDTO();
        expectedResult.setSuccess(false);

        MakeReverseProvisionRequest makeReverseProvisionRequest = new MakeReverseProvisionRequest();
        makeReverseProvisionRequest.setContractNo(inputDto.getContractNo());
        makeReverseProvisionRequest.setTransactionId(inputDto.getChannelTransactionId());
        makeReverseProvisionRequest.setReverseDescriptionAppendix("İPTAL");

        MakeReverseProvisionResponse makeDto = new MakeReverseProvisionResponse();
        makeDto.setErrorCode(500L);
        makeDto.setSuccess(false);

        ServiceCallException serviceCallException = new ServiceCallException(new ExceptionData());
        RuntimeException runtimeException = new RuntimeException(serviceCallException);

        Mockito.when(provisionNextService.makeReverseProvision(Mockito.argThat(argument ->
                argument.getContractNo().equals(makeReverseProvisionRequest.getContractNo()) &&
                        argument.getTransactionId().equals(makeReverseProvisionRequest.getTransactionId()) &&
                        argument.getReverseDescriptionAppendix().equals(makeReverseProvisionRequest.getReverseDescriptionAppendix())
        ))).thenThrow(runtimeException);

        CreateReverseAccountingResultDTO actualResult = cardReverseProvisionService.doReverseAccounting(inputDto);
        assertEquals(expectedResult.isSuccess(), actualResult.isSuccess());
        verify(provisionNextService).makeReverseProvision(Mockito.argThat(argument ->
                argument.getContractNo().equals(makeReverseProvisionRequest.getContractNo()) &&
                        argument.getTransactionId().equals(makeReverseProvisionRequest.getTransactionId()) &&
                        argument.getReverseDescriptionAppendix().equals(makeReverseProvisionRequest.getReverseDescriptionAppendix())
        ));
    }












}" 


 FAKAT "
			if(!makeReverseProvision.isSuccess()){
				handleException(makeReverseProvision.getErrorCode(), createReverseAccountingResultDTO);
				createReverseAccountingResultDTO.setSuccess(false);
				return createReverseAccountingResultDTO;
			}
			createReverseAccountingResultDTO.setSuccess(true);" BU PARÇA VE "			createReverseAccountingResultDTO.setError(EnumBillResult.GENERIC_UNKNOWN_ERROR);
			createReverseAccountingResultDTO.setSuccess(false);
		}

		return createReverseAccountingResultDTO;
	}"
