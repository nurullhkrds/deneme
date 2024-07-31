import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import java.math.BigDecimal;
import java.time.LocalDate;
import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.List;

class ReceiptServiceImplTest {

    @Mock
    private ReceiptApiService receiptService;

    @InjectMocks
    private ReceiptServiceImpl receiptServiceImpl;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testPrintReceiptWithProvisionTypeCardAndDummyMerchantFalse() {
        // Arrange
        CreateAccountingDTO createAccountingDTO = createAccountingDTO(EnumProvisionType.CARD, false);
        CreateAccountingResultDTO createAccountingResultDTO = createAccountingResultDTO(BigDecimal.TEN.negate(), true);

        // Act
        receiptServiceImpl.printReceipt(createAccountingDTO, createAccountingResultDTO);

        // Assert
        verify(receiptService, times(1)).printReceipt(anyList());
    }

    @Test
    void testPrintReceiptWithProvisionTypeCardAndDummyMerchantTrue() {
        // Arrange
        CreateAccountingDTO createAccountingDTO = createAccountingDTO(EnumProvisionType.CARD, true);
        CreateAccountingResultDTO createAccountingResultDTO = createAccountingResultDTO(BigDecimal.TEN.negate(), true);

        // Act
        receiptServiceImpl.printReceipt(createAccountingDTO, createAccountingResultDTO);

        // Assert
        verify(receiptService, times(1)).printReceipt(anyList());
    }

    @Test
    void testPrintReceiptWithProvisionTypeAccountAndEmptyPendingDetailList() {
        // Arrange
        CreateAccountingDTO createAccountingDTO = createAccountingDTO(EnumProvisionType.ACCOUNT, false);
        CreateAccountingResultDTO createAccountingResultDTO = createAccountingResultDTO(BigDecimal.TEN.negate(), false);

        // Act
        receiptServiceImpl.printReceipt(createAccountingDTO, createAccountingResultDTO);

        // Assert
        verify(receiptService, times(1)).printReceipt(anyList());
    }

    @Test
    void testPrintReceiptWithProvisionTypeAccountAndFilledPendingDetailList() {
        // Arrange
        CreateAccountingDTO createAccountingDTO = createAccountingDTO(EnumProvisionType.ACCOUNT, false);
        CreateAccountingResultDTO createAccountingResultDTO = createAccountingResultDTO(BigDecimal.TEN.negate(), true);

        // Act
        receiptServiceImpl.printReceipt(createAccountingDTO, createAccountingResultDTO);

        // Assert
        verify(receiptService, times(1)).printReceipt(anyList());
    }

    // Helper methods for setting up the DTOs

    private CreateAccountingDTO createAccountingDTO(EnumProvisionType provisionType, boolean isDummyMerchant) {
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        PaymentMethodType paymentMethodType = new PaymentMethodType();
        paymentMethodType.setProvisionType(provisionType);
        createAccountingDTO.setPaymentMethodType(paymentMethodType);

        if (provisionType == EnumProvisionType.ACCOUNT) {
            AccountPaymentMethodDetailDTO accountPaymentMethodDetailDTO = new AccountPaymentMethodDetailDTO();
            accountPaymentMethodDetailDTO.setAccountNo("123456");
            createAccountingDTO.setPaymentMethodDetailDTO(accountPaymentMethodDetailDTO);
        } else if (provisionType == EnumProvisionType.CARD) {
            CreditCardPaymentMethodDetailDTO creditCardPaymentMethodDetailDTO = new CreditCardPaymentMethodDetailDTO();
            creditCardPaymentMethodDetailDTO.setCardNumber("1234-5678-9101-1121");
            createAccountingDTO.setPaymentMethodDetailDTO(creditCardPaymentMethodDetailDTO);
        }

        createAccountingDTO.setBranchCode("001");
        createAccountingDTO.setProvisionDTO(createProvisionDTO());
        createAccountingDTO.setInstitutionAccountingInfoDTO(createInstitutionAccountingInfoDTO());
        createAccountingDTO.setCurrency(createCurrencyDTO());
        createAccountingDTO.setAgentCode("AGENT123");
        createAccountingDTO.setChannelCode("CHANNEL123");
        createAccountingDTO.setChannelTransactionId("TXN123");
        createAccountingDTO.setDummyMerchant(isDummyMerchant);

        return createAccountingDTO;
    }

    private CreateAccountingResultDTO createAccountingResultDTO(BigDecimal totalPaymentAmount, boolean hasPendingDetails) {
        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();
        createAccountingResultDTO.setTotalPaymentAmount(totalPaymentAmount);
        createAccountingResultDTO.setContractNo("CONTRACT123");

        if (hasPendingDetails) {
            List<ProvisionDetailDTO> pendingDetailList = new ArrayList<>();
            ProvisionDetailDTO provisionDetailDTO = new ProvisionDetailDTO();
            provisionDetailDTO.setAmount(BigDecimal.ONE.negate());
            provisionDetailDTO.setPendingTransactionDetailId(1L);
            pendingDetailList.add(provisionDetailDTO);
            createAccountingResultDTO.setPendingDetailList(pendingDetailList);
        } else {
            createAccountingResultDTO.setPendingDetailList(new ArrayList<>());
        }

        return createAccountingResultDTO;
    }

    private ProvisionDTO createProvisionDTO() {
        ProvisionDTO provisionDTO = new ProvisionDTO();
        provisionDTO.setCustomerNo(123L);
        provisionDTO.setSubscriberNo("SUBSCRIBER123");
        provisionDTO.setBillNo("BILL123");
        provisionDTO.setBillTerm("TERM123");
        provisionDTO.setBillDueDate(LocalDate.now());
        return provisionDTO;
    }

    private InstitutionAccountingInfoDTO createInstitutionAccountingInfoDTO() {
        InstitutionAccountingInfoDTO institutionAccountingInfoDTO = new InstitutionAccountingInfoDTO();
        institutionAccountingInfoDTO.setReceiptCode("RECEIPTCODE123");
        return institutionAccountingInfoDTO;
    }

    private CurrencyDTO createCurrencyDTO() {
        CurrencyDTO currencyDTO = new CurrencyDTO();
        currencyDTO.setValue("USD");
        return currencyDTO;
    }
}
