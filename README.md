import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.*;

import java.math.BigDecimal;
import java.util.Collections;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.mockito.junit.jupiter.MockitoExtension;

@ExtendWith(MockitoExtension.class)
public class AccountingServiceTest {

    @Mock
    private ProvisionFactory provisionFactory;

    @Mock
    private ProvisionService provisionService;

    @Mock
    private ReceiptService receiptService;

    @InjectMocks
    private AccountingService accountingService; // Bu sınıfın adını değiştirin, orijinal sınıf ismi ile aynı olmalı

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this); // Mockito nesnelerini başlatır
        when(provisionFactory.getProvisionService(any())).thenReturn(provisionService);
        when(provisionService.doAccounting(any())).thenReturn(createAccountingResultDTO(true));
    }

    @Test
    public void testDoAccounting_SuccessfulAccounting() {
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        createAccountingDTO.setPaymentAmount(BigDecimal.valueOf(100));
        createAccountingDTO.setPaymentMethodType(EnumPaymentMethod.CREDIT_CARD); // EnumPaymentMethod'dan bir örnek kullanın
        ResponseCommissionInformation responseCommissionInformation = new ResponseCommissionInformation();
        responseCommissionInformation.setTotalCommissionLocalCurrencyAmount(BigDecimal.valueOf(10));
        responseCommissionInformation.setTotalCommissionTaxLocalCurrencyAmount(BigDecimal.valueOf(2));
        createAccountingDTO.setResponseCommissionInformation(responseCommissionInformation);

        CreateAccountingResultDTO result = accountingService.doAccounting(createAccountingDTO);

        assertTrue(result.isSuccess());
        assertEquals(BigDecimal.valueOf(12), result.getCommissionAmount());
        assertEquals(BigDecimal.valueOf(112), result.getTotalPaymentAmount());

        verify(receiptService, times(1)).printReceipt(createAccountingDTO, result);
    }

    @Test
    public void testDoAccounting_NoCommission() {
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        createAccountingDTO.setPaymentAmount(BigDecimal.valueOf(100));
        createAccountingDTO.setPaymentMethodType(EnumPaymentMethod.CREDIT_CARD); // EnumPaymentMethod'dan bir örnek kullanın

        CreateAccountingResultDTO result = accountingService.doAccounting(createAccountingDTO);

        assertTrue(result.isSuccess());
        assertEquals(BigDecimal.ZERO, result.getCommissionAmount());
        assertEquals(BigDecimal.valueOf(100), result.getTotalPaymentAmount());

        verify(receiptService, times(1)).printReceipt(createAccountingDTO, result);
    }

    private CreateAccountingResultDTO createAccountingResultDTO(boolean isSuccess) {
        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();
        createAccountingResultDTO.setSuccess(isSuccess);
        createAccountingResultDTO.setPendingDetailList(Collections.emptyList());
        return createAccountingResultDTO;
    }
}
