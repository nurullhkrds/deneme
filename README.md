import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.ArgumentCaptor;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.junit.jupiter.api.extension.ExtendWith;
import com.ykb.payments.bill.transaction.accounting.provision.service.CardProvisionServiceImpl;
import com.ykb.payments.bill.transaction.accounting.provision.dto.*;

@ExtendWith(MockitoExtension.class)
public class CardProvisionServiceImplTest {

    @Mock
    private SwtSwitchIntegrationService cardProvisionService;

    @Mock
    private ProvisionNextService provisionNextService;

    @Mock
    private AccountingUtilServiceImpl accountingUtilServiceImpl;

    @Mock
    private AccountingUtil accountingDateUtil;

    @InjectMocks
    private CardProvisionServiceImpl cardProvisionServiceImpl;

    private CreateAccountingDTO createAccountingDTO;

    @BeforeEach
    void setUp() {
        createAccountingDTO = new CreateAccountingDTO();
        // Setup createAccountingDTO with necessary values
        // Same as your setup method
        createAccountingDTO.setDummyMerchant(true);
    }

    @Test
    void testDoAccounting_PrepareProvisionRequest() {
        createAccountingDTO.setDummyMerchant(true);

        MakeProvisionResponse makeProvisionResponse = new MakeProvisionResponse();
        makeProvisionResponse.setSuccess(true);
        makeProvisionResponse.setContractNo(123456L);
        when(provisionNextService.makeProvision(any(MakeProvisionRequest.class)))
                .thenReturn(makeProvisionResponse);

        CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);

        // Ensure the method succeeds
        assertTrue(result.isSuccess());

        // Capture the argument
        ArgumentCaptor<MakeProvisionRequest> argumentCaptor = ArgumentCaptor.forClass(MakeProvisionRequest.class);
        verify(provisionNextService).makeProvision(argumentCaptor.capture());
        MakeProvisionRequest capturedRequest = argumentCaptor.getValue();

        // Verify the captured values
        assertEquals(createAccountingDTO.getChannelTransactionId(), capturedRequest.getTransactionId());
    }
}
