import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import java.math.BigDecimal;
import java.time.LocalDate;
import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.List;

@ExtendWith(MockitoExtension.class)
public class ReceiptServiceImplTest {

    @Mock
    private ReceiptApiService receiptService;

    @InjectMocks
    private ReceiptServiceImpl receiptServiceImpl;

    private CreateAccountingDTO createAccountingDTO;
    private CreateAccountingResultDTO createAccountingResultDTO;

    @BeforeEach
    void setUp() {
        createAccountingDTO = new CreateAccountingDTO();
        createAccountingResultDTO = new CreateAccountingResultDTO();
        
        // Mock necessary sub-objects and set their properties
        PaymentMethodType paymentMethodType = mock(PaymentMethodType.class);
        when(paymentMethodType.getProvisionType()).thenReturn(EnumProvisionType.ACCOUNT);
        createAccountingDTO.setPaymentMethodType(paymentMethodType);
        createAccountingDTO.setDummyMerchant(false);
        
        ProvisionDTO provisionDTO = mock(ProvisionDTO.class);
        createAccountingDTO.setProvisionDTO(provisionDTO);
        
        InstitutionAccountingInfoDTO institutionAccountingInfoDTO = mock(InstitutionAccountingInfoDTO.class);
        createAccountingDTO.setInstitutionAccountingInfoDTO(institutionAccountingInfoDTO);

        Institution institution = mock(Institution.class);
        createAccountingDTO.setInstitution(institution);
    }

    @Test
    void testPrintReceipt_withCredit() {
        // Mocking the internal method calls indirectly
        doCallRealMethod().when(receiptServiceImpl).printReceipt(any(CreateAccountingDTO.class), any(CreateAccountingResultDTO.class));

        // Execute the method to be tested
        receiptServiceImpl.printReceipt(createAccountingDTO, createAccountingResultDTO);

        // Verify that the receiptService.printReceipt method was called with the expected number of receipts
        verify(receiptService, times(1)).printReceipt(argThat(receipts -> receipts.size() == 2));
    }

    @Test
    void testPrintReceipt_withoutCredit() {
        // Set up the condition to avoid credit receipt
        when(createAccountingDTO.getPaymentMethodType().getProvisionType()).thenReturn(EnumProvisionType.CARD);
        when(createAccountingDTO.isDummyMerchant()).thenReturn(true);

        // Mocking the internal method calls indirectly
        doCallRealMethod().when(receiptServiceImpl).printReceipt(any(CreateAccountingDTO.class), any(CreateAccountingResultDTO.class));

        // Execute the method to be tested
        receiptServiceImpl.printReceipt(createAccountingDTO, createAccountingResultDTO);

        // Verify that the receiptService.printReceipt method was called with the expected number of receipts
        verify(receiptService, times(1)).printReceipt(argThat(receipts -> receipts.size() == 1));
    }

    @Test
    void testDebitReceiptDetailsPreparation() {
        // Test the details preparation indirectly through printReceipt
        // Modify the createAccountingDTO and createAccountingResultDTO to have specific values
        when(createAccountingDTO.getInstitutionAccountingInfoDTO().getReceiptCode()).thenReturn("SOME_CODE");

        receiptServiceImpl.printReceipt(createAccountingDTO, createAccountingResultDTO);

        // Verify that the internal methods were called with expected arguments
        verify(receiptService, times(1)).printReceipt(argThat(receipts -> {
            RequestApiReceiptDTO debitReceipt = receipts.get(0);
            assertNotNull(debitReceipt.getReceiptDetailList());
            // Add more assertions based on expected details
            return true;
        }));
    }

    @Test
    void testCreditReceiptDetailsPreparation() {
        // Test the details preparation indirectly through printReceipt
        // Modify the createAccountingDTO and createAccountingResultDTO to have specific values
        when(createAccountingDTO.getInstitutionAccountingInfoDTO().getReceiptCode()).thenReturn("SOME_CODE");

        receiptServiceImpl.printReceipt(createAccountingDTO, createAccountingResultDTO);

        // Verify that the internal methods were called with expected arguments
        verify(receiptService, times(1)).printReceipt(argThat(receipts -> {
            if (receipts.size() == 2) {
                RequestApiReceiptDTO creditReceipt = receipts.get(1);
                assertNotNull(creditReceipt.getReceiptDetailList());
                // Add more assertions based on expected details
            }
            return true;
        }));
    }

    @Test
    void testDescriptionPreparation() {
        // Modify the createAccountingDTO to have specific values
        when(createAccountingDTO.getInstitution().getName()).thenReturn("Test Institution");
        when(createAccountingDTO.getProvisionDTO().getSubscriberNo()).thenReturn("12345");

        receiptServiceImpl.printReceipt(createAccountingDTO, createAccountingResultDTO);

        // Verify the description
        verify(receiptService, times(1)).printReceipt(argThat(receipts -> {
            RequestApiReceiptDTO debitReceipt = receipts.get(0);
            assertEquals("Test Institution-12345", debitReceipt.getDescription());
            return true;
        }));
    }
}
