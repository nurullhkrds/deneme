import java.math.BigDecimal;
import java.util.Collections;
import java.util.Date;
import java.util.List;

import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.verify;

public class ReceiptServiceImplTest {

    @Mock
    private ReceiptApiService receiptApiService;

    @InjectMocks
    private ReceiptServiceImpl receiptServiceImpl;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testPrintReceiptWithAccountPaymentMethod() {
        // Arrange
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();

        AccountPaymentMethodDetailDTO accountDetailDTO = new AccountPaymentMethodDetailDTO();
        accountDetailDTO.setAccountNo("123456789");

        createAccountingDTO.setPaymentMethodType(EnumPaymentMethod.ACCOUNT);
        createAccountingDTO.setPaymentMethodDetailDTO(accountDetailDTO);
        createAccountingDTO.setBranchCode("001");
        createAccountingDTO.setAgentCode("AGENT001");
        createAccountingDTO.setChannelCode("CH001");
        createAccountingDTO.setCurrency(EnumCurrencyCode.DOLAR);
        createAccountingDTO.setProvisionDTO(new ProvisionDTO());
        createAccountingDTO.getProvisionDTO().setCustomerNo(1001L);
        createAccountingDTO.setInstitutionAccountingInfoDTO(new InstitutionAccountingInfoDTO());
        createAccountingDTO.getInstitutionAccountingInfoDTO().setReceiptCode("RECEIPT_CODE");
        createAccountingDTO.setInstitution(new InstitutionDTO()); // Ensure InstitutionDTO is not null
        createAccountingDTO.getInstitution().setName("Institution Name");

        createAccountingResultDTO.setContractNo(123456789L);
        createAccountingResultDTO.setTotalPaymentAmount(BigDecimal.valueOf(100.00));
        createAccountingResultDTO.setPendingDetailList(Collections.singletonList(new ProvisionDetailDTO()));
        createAccountingResultDTO.setAvailableDate(new Date()); // Set a valid date

        // Act
        receiptServiceImpl.printReceipt(createAccountingDTO, createAccountingResultDTO);

        // Assert
        verify(receiptApiService).printReceipt(any(List.class));
    }

    @Test
    void testPrintReceiptWithCardPaymentMethod() {
        // Arrange
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();

        CreditCardPaymentMethodDetailDTO cardDetailDTO = new CreditCardPaymentMethodDetailDTO();
        cardDetailDTO.setCardNumber("4111111111111111");

        createAccountingDTO.setPaymentMethodType(EnumPaymentMethod.CARD);
        createAccountingDTO.setPaymentMethodDetailDTO(cardDetailDTO);
        createAccountingDTO.setBranchCode("002");
        createAccountingDTO.setAgentCode("AGENT002");
        createAccountingDTO.setChannelCode("CH002");
        createAccountingDTO.setCurrency(EnumCurrencyCode.DOLAR);
        createAccountingDTO.setProvisionDTO(new ProvisionDTO());
        createAccountingDTO.getProvisionDTO().setCustomerNo(1002L);
        createAccountingDTO.setInstitutionAccountingInfoDTO(new InstitutionAccountingInfoDTO());
        createAccountingDTO.getInstitutionAccountingInfoDTO().setReceiptCode("RECEIPT_CODE");
        createAccountingDTO.setInstitution(new InstitutionDTO()); // Ensure InstitutionDTO is not null
        createAccountingDTO.getInstitution().setName("Institution Name");

        createAccountingResultDTO.setContractNo(234567890L);
        createAccountingResultDTO.setTotalPaymentAmount(BigDecimal.valueOf(200.00));
        createAccountingResultDTO.setPendingDetailList(Collections.singletonList(new ProvisionDetailDTO()));
        createAccountingResultDTO.setAvailableDate(new Date()); // Set a valid date

        // Act
        receiptServiceImpl.printReceipt(createAccountingDTO, createAccountingResultDTO);

        // Assert
        verify(receiptApiService).printReceipt(any(List.class));
    }

    @Test
    void testPrintReceiptWithCardPaymentMethodAndDummyMerchant() {
        // Arrange
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();

        CreditCardPaymentMethodDetailDTO cardDetailDTO = new CreditCardPaymentMethodDetailDTO();
        cardDetailDTO.setCardNumber("4111111111111111");

        createAccountingDTO.setPaymentMethodType(EnumPaymentMethod.CARD);
        createAccountingDTO.setPaymentMethodDetailDTO(cardDetailDTO);
        createAccountingDTO.setBranchCode("004");
        createAccountingDTO.setAgentCode("AGENT004");
        createAccountingDTO.setChannelCode("CH004");
        createAccountingDTO.setCurrency(EnumCurrencyCode.DOLAR);
        createAccountingDTO.setProvisionDTO(new ProvisionDTO());
        createAccountingDTO.getProvisionDTO().setCustomerNo(1004L);
        createAccountingDTO.setInstitutionAccountingInfoDTO(new InstitutionAccountingInfoDTO());
        createAccountingDTO.getInstitutionAccountingInfoDTO().setReceiptCode("RECEIPT_CODE");
        createAccountingDTO.setDummyMerchant(true);
        createAccountingDTO.setInstitution(new InstitutionDTO()); // Ensure InstitutionDTO is not null
        createAccountingDTO.getInstitution().setName("Institution Name");

        createAccountingResultDTO.setContractNo(456789012L);
        createAccountingResultDTO.setTotalPaymentAmount(BigDecimal.valueOf(400.00));
        createAccountingResultDTO.setPendingDetailList(Collections.singletonList(new ProvisionDetailDTO()));
        createAccountingResultDTO.setAvailableDate(new Date()); // Set a valid date

        // Act
        receiptServiceImpl.printReceipt(createAccountingDTO, createAccountingResultDTO);

        // Assert
        verify(receiptApiService).printReceipt(any(List.class));
    }

    @Test
    void testPrintReceiptWithCashPaymentMethod() {
        // Arrange
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();

        createAccountingDTO.setPaymentMethodType(EnumPaymentMethod.CASH);
        createAccountingDTO.setBranchCode("005");
        createAccountingDTO.setAgentCode("AGENT005");
        createAccountingDTO.setChannelCode("CH005");
        createAccountingDTO.setCurrency(EnumCurrencyCode.DOLAR);
        createAccountingDTO.setProvisionDTO(new ProvisionDTO());
        createAccountingDTO.getProvisionDTO().setCustomerNo(1005L);
        createAccountingDTO.setInstitutionAccountingInfoDTO(new InstitutionAccountingInfoDTO());
        createAccountingDTO.getInstitutionAccountingInfoDTO().setReceiptCode("RECEIPT_CODE");
        createAccountingDTO.setInstitution(new InstitutionDTO()); // Ensure InstitutionDTO is not null
        createAccountingDTO.getInstitution().setName("Institution Name");

        createAccountingResultDTO.setContractNo(567890123L);
        createAccountingResultDTO.setTotalPaymentAmount(BigDecimal.valueOf(500.00));
        createAccountingResultDTO.setPendingDetailList(Collections.singletonList(new ProvisionDetailDTO()));
        createAccountingResultDTO.setAvailableDate(new Date()); // Set a valid date

        // Act
        receiptServiceImpl.printReceipt(createAccountingDTO, createAccountingResultDTO);

        // Assert
        verify(receiptApiService).printReceipt(any(List.class));
    }
}
