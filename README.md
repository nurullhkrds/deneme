  @InjectMocks
    private AccountingServiceImpl accountingService;

    @Mock
    private ProvisionFactory provisionFactory;

    @Mock
    private ReverseProvisionFactory reverseProvisionFactory;

    @Mock
    private ReceiptService receiptService;

    @Mock
    private ProvisionService provisionService;

    @Mock
    private ReverseProvisionService reverseProvisionService;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    public void testDoAccountingSuccess() {
        // Arrange
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();
        createAccountingResultDTO.setSuccess(true);
        ProvisionService provisionService = mock(ProvisionService.class);
        
        when(provisionFactory.getProvisionService(any())).thenReturn(provisionService);
        when(provisionService.doAccounting(any())).thenReturn(createAccountingResultDTO);

        // Act
        CreateAccountingResultDTO result = accountingService.doAccounting(createAccountingDTO);

        // Assert
        verify(provisionFactory).getProvisionService(any());
        verify(provisionService).doAccounting(any());
        verify(receiptService).printReceipt(any(), any());
        assertTrue(result.isSuccess());
    }

    @Test
    public void testDoAccountingFailure() {
        // Arrange
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();
        createAccountingResultDTO.setSuccess(false);
        ProvisionService provisionService = mock(ProvisionService.class);

        when(provisionFactory.getProvisionService(any())).thenReturn(provisionService);
        when(provisionService.doAccounting(any())).thenReturn(createAccountingResultDTO);

        // Act
        CreateAccountingResultDTO result = accountingService.doAccounting(createAccountingDTO);

        // Assert
        verify(provisionFactory).getProvisionService(any());
        verify(provisionService).doAccounting(any());
        verify(receiptService, never()).printReceipt(any(), any());
        assertFalse(result.isSuccess());
    }

    @Test
    public void testDoReverseAccounting() {
        // Arrange
        CreateReverseAccountingDTO createReverseAccountingDTO = new CreateReverseAccountingDTO();
        CreateReverseAccountingResultDTO createReverseAccountingResultDTO = new CreateReverseAccountingResultDTO();
        when(reverseProvisionFactory.getReverseProvisionService(any())).thenReturn(reverseProvisionService);
        when(reverseProvisionService.doReverseAccounting(any())).thenReturn(createReverseAccountingResultDTO);

        // Act
        CreateReverseAccountingResultDTO result = accountingService.doReverseAccounting(createReverseAccountingDTO);

        // Assert
        verify(reverseProvisionFactory).getReverseProvisionService(any());
        verify(reverseProvisionService).doReverseAccounting(any());
        assertEquals(createReverseAccountingResultDTO, result);
    }

    @Test
    public void testPrepareCreateAccountingDTO() throws JsonProcessingException {
        // Arrange
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        ProvisionDTO provisionDTO = new ProvisionDTO();
        String commissionData = "{\"someField\": \"someValue\"}";
        provisionDTO.setCommissionData(commissionData);
        createAccountingDTO.setProvisionDTO(provisionDTO);

        ObjectMapper objectMapper = mock(ObjectMapper.class);
        ResponseCommissionInformation responseCommissionInformation = new ResponseCommissionInformation();
        when(objectMapper.readValue(commissionData, ResponseCommissionInformation.class))
                .thenReturn(responseCommissionInformation);

        // Act
        CreateAccountingDTO resultDTO = accountingService.prepareCreateAccountingDTO(createAccountingDTO);

        // Assert
        assertNotNull(resultDTO.getResponseCommissionInformation());
        assertEquals(responseCommissionInformation, resultDTO.getResponseCommissionInformation());
    }

    @Test
    public void testPrepareCreateAccountingResultDTO() {
        // Arrange
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        createAccountingDTO.setPaymentAmount(BigDecimal.valueOf(100));
        ResponseCommissionInformation responseCommissionInformation = new ResponseCommissionInformation();
        responseCommissionInformation.setTotalCommissionLocalCurrencyAmount(BigDecimal.valueOf(10));
        responseCommissionInformation.setTotalCommissionTaxLocalCurrencyAmount(BigDecimal.valueOf(5));
        createAccountingDTO.setResponseCommissionInformation(responseCommissionInformation);

        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();
        createAccountingResultDTO.setPendingDetailList(new ArrayList<>());

        // Act
        CreateAccountingResultDTO resultDTO = accountingService.prepareCreateAccountingResultDTO(createAccountingResultDTO, createAccountingDTO);

        // Assert
        assertEquals(BigDecimal.valueOf(115), resultDTO.getTotalPaymentAmount());
        assertEquals(BigDecimal.valueOf(15), resultDTO.getCommissionAmount());
    }
