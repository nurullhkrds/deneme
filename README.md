import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import static org.mockito.Mockito.*;

import java.math.BigDecimal;
import java.time.LocalDate;
import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.Collections;
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
    public void setUp() {
        createAccountingDTO = new CreateAccountingDTO();
        createAccountingResultDTO = new CreateAccountingResultDTO();

        PaymentMethodTypeDTO paymentMethodTypeDTO = new PaymentMethodTypeDTO();
        paymentMethodTypeDTO.setProvisionType(EnumProvisionType.CARD);
        createAccountingDTO.setPaymentMethodType(paymentMethodTypeDTO);
        createAccountingDTO.setDummyMerchant(false);
        createAccountingDTO.setCurrency(new CurrencyDTO("USD"));
        createAccountingDTO.setBranchCode("001");
        createAccountingDTO.setAgentCode("agentCode");
        createAccountingDTO.setChannelCode("channelCode");
        createAccountingDTO.setChannelTransactionId("123456");
        createAccountingDTO.setProvisionDTO(new ProvisionDTO());
        createAccountingDTO.getProvisionDTO().setCustomerNo(123L);
        createAccountingDTO.getProvisionDTO().setBillDueDate(LocalDate.now());
        createAccountingDTO.getProvisionDTO().setSubscriberNo("subscriberNo");
        createAccountingResultDTO.setContractNo("contractNo");
        createAccountingResultDTO.setTotalPaymentAmount(BigDecimal.valueOf(1000));
        createAccountingResultDTO.setPendingDetailList(Collections.emptyList());
    }

    @Test
    public void testPrintReceipt() {
        receiptServiceImpl.printReceipt(createAccountingDTO, createAccountingResultDTO);
        verify(receiptService, times(1)).printReceipt(anyList());
    }

    @Test
    public void testPrintReceiptWithAccountAndDummyMerchantFalse() {
        createAccountingDTO.getPaymentMethodType().setProvisionType(EnumProvisionType.ACCOUNT);
        createAccountingDTO.setDummyMerchant(false);

        receiptServiceImpl.printReceipt(createAccountingDTO, createAccountingResultDTO);
        verify(receiptService, times(1)).printReceipt(anyList());
    }

    @Test
    public void testPrintReceiptWithAccountAndDummyMerchantTrue() {
        createAccountingDTO.getPaymentMethodType().setProvisionType(EnumProvisionType.ACCOUNT);
        createAccountingDTO.setDummyMerchant(true);

        receiptServiceImpl.printReceipt(createAccountingDTO, createAccountingResultDTO);
        verify(receiptService, times(1)).printReceipt(anyList());
    }

    @Test
    public void testPrintReceiptNullPointerException() {
        createAccountingDTO = null;

        assertThrows(NullPointerException.class, () -> {
            receiptServiceImpl.printReceipt(createAccountingDTO, createAccountingResultDTO);
        });
    }

    @Test
    public void testPrintReceiptWithPendingDetailList() {
        ProvisionDetailDTO provisionDetailDTO = new ProvisionDetailDTO();
        provisionDetailDTO.setAccountingDate(DateUtils.getCurrentDate());
        provisionDetailDTO.setAmount(BigDecimal.valueOf(-100));
        provisionDetailDTO.setPendingTransactionDetailId(123L);

        createAccountingResultDTO.setPendingDetailList(Collections.singletonList(provisionDetailDTO));

        receiptServiceImpl.printReceipt(createAccountingDTO, createAccountingResultDTO);
        verify(receiptService, times(1)).printReceipt(anyList());
    }

    @Test
    public void testPrepareReceiptDebitDetailsWithDifferentReceiptFieldTypes() {
        List<RequestReceiptDetailDTO> receiptDetailList = new ArrayList<>();

        for (EnumPymBill pymBill : EnumPymBill.values()) {
            prepareRequestReceiptDetailDTO(pymBill.getLblFieldOrder(), pymBill.getValueFieldOrder(), pymBill.getReceiptFieldType(),
                    pymBill.getSourceType(), createAccountingDTO, createAccountingResultDTO, receiptDetailList);
        }

        receiptDetailList = receiptServiceImpl.prepareReceiptDebitDetails(createAccountingDTO, createAccountingResultDTO);

        assertNotNull(receiptDetailList);
        // Additional assertions can be added based on the logic in prepareReceiptDebitDetails
    }

    @Test
    public void testPrepareDebitRequestApiReceiptDTO() {
        RequestApiReceiptDTO debitRequestApiReceiptDTO = receiptServiceImpl.prepareDebitRequestApiReceiptDTO(createAccountingDTO, createAccountingResultDTO);
        assertNotNull(debitRequestApiReceiptDTO);
    }

    @Test
    public void testPrepareReceiptDebitMaster() {
        RequestApiReceiptDTO requestApiReceiptDTO = receiptServiceImpl.prepareReceiptDebitMaster(createAccountingDTO, createAccountingResultDTO);
        assertNotNull(requestApiReceiptDTO);
    }

    @Test
    public void testPrepareReceiptDebitDetails() {
        List<RequestReceiptDetailDTO> receiptDetailList = receiptServiceImpl.prepareReceiptDebitDetails(createAccountingDTO, createAccountingResultDTO);
        assertNotNull(receiptDetailList);
    }

    @Test
    public void testPrepareCreditRequestApiReceiptDTO() {
        RequestApiReceiptDTO creditRequestApiReceiptDTO = receiptServiceImpl.prepareCreditRequestApiReceiptDTO(createAccountingDTO, createAccountingResultDTO);
        assertNotNull(creditRequestApiReceiptDTO);
    }
}
