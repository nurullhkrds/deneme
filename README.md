import org.junit.Before;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

public class YourTestClass {

    @Mock
    private ProvisionNextService provisionNextService;

    @InjectMocks
    private AccountReverseProvisionService accountReverseProvisionService;

    @Before
    public void setup() {
        MockitoAnnotations.initMocks(this);
    }

    @Test
    public void testDoReverseAccounting_GeneralException() throws ServiceCallException {
        // Mock a general exception thrown
        CreateReverseAccountingDTO inputDTO = new CreateReverseAccountingDTO();
        inputDTO.setChannelTransactionId("12345");
        inputDTO.setContractNo(987654L);

        // Mock nesne yapılandırması
        when(provisionNextService.makeReverseProvision(any())).thenThrow(new RuntimeException("Test exception"));

        // Invoke the method under test
        CreateReverseAccountingResultDTO resultDTO = accountReverseProvisionService.doReverseAccounting(inputDTO);

        // Verify the result
        verify(provisionNextService, times(1)).makeReverseProvision(any());
        assertFalse(resultDTO.isSuccess());
        assertNotNull(resultDTO.getError()); // null kontrolü için assertNotNull kullanılabilir
    }
}
