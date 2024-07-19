
public class AccountReverseProvisionServiceImplTest {

    @InjectMocks
    private AccountReverseProvisionServiceImpl accountReverseProvisionServiceImpl;

    @Mock
    private ProvisionNextService provisionNextService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testDoReverseAccounting_Success() {
        // Arrange
        CreateReverseAccountingDTO inputDTO = new CreateReverseAccountingDTO();
        inputDTO.setChannelTransactionId("txn123");
        inputDTO.setContractNo(12345L);

        MakeReverseProvisionRequest request = new MakeReverseProvisionRequest();
        request.setTransactionId("txn123");
        request.setContractNo(12345L);
        request.setReverseDescriptionAppendix("İPTAL");

        MakeReverseProvisionResponse response = new MakeReverseProvisionResponse();
        response.setSuccess(true);

        when(provisionNextService.makeReverseProvision(any(MakeReverseProvisionRequest.class)))
                .thenReturn(response);

        // Act
        CreateReverseAccountingResultDTO result = accountReverseProvisionServiceImpl.doReverseAccounting(inputDTO);

        // Assert
        assertTrue(result.isSuccess());
        assertNull(result.getError());
    }

    @Test
    void testDoReverseAccounting_Failure() {
        // Arrange
        CreateReverseAccountingDTO inputDTO = new CreateReverseAccountingDTO();
        inputDTO.setChannelTransactionId("txn123");
        inputDTO.setContractNo(12345L);

        MakeReverseProvisionRequest request = new MakeReverseProvisionRequest();
        request.setTransactionId("txn123");
        request.setContractNo(12345L);
        request.setReverseDescriptionAppendix("İPTAL");

        MakeReverseProvisionResponse response = new MakeReverseProvisionResponse();
        response.setSuccess(false);
        response.setErrorCode(100L);

        when(provisionNextService.makeReverseProvision(any(MakeReverseProvisionRequest.class)))
                .thenReturn(response);

        // Act
        CreateReverseAccountingResultDTO result = accountReverseProvisionServiceImpl.doReverseAccounting(inputDTO);

        // Assert
        assertFalse(result.isSuccess());
        assertEquals(EnumBillResult.SOME_ERROR_CODE, result.getError()); // Change to the actual expected error code
    }

    @Test
    void testDoReverseAccounting_ExceptionHandling() {
        // Arrange
        CreateReverseAccountingDTO inputDTO = new CreateReverseAccountingDTO();
        inputDTO.setChannelTransactionId("txn123");
        inputDTO.setContractNo(12345L);

        when(provisionNextService.makeReverseProvision(any(MakeReverseProvisionRequest.class)))
                .thenThrow(new ServiceCallException(200L));

        // Act
        CreateReverseAccountingResultDTO result = accountReverseProvisionServiceImpl.doReverseAccounting(inputDTO);

        // Assert
        assertFalse(result.isSuccess());
        assertEquals(EnumBillResult.SOME_OTHER_ERROR_CODE, result.getError()); // Change to the actual expected error code
    }
}
