import static org.mockito.ArgumentMatchers.argThat;
import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

import java.math.BigDecimal;
import java.time.LocalDate;
import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

public class ReceiptServiceImplTest {

    @Mock
    private ReceiptApiService receiptApiService;

    @InjectMocks
    private ReceiptServiceImpl receiptServiceImpl;

    private CreateAccountingDTO createAccountingDTO;
    private CreateAccountingResultDTO createAccountingResultDTO;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);

        createAccountingDTO = new CreateAccountingDTO();
        createAccountingResultDTO = new CreateAccountingResultDTO();

        // Genel varsayılan ayarlar
        createAccountingDTO.setBranchCode("BR001");
        createAccountingDTO.setProvisionDTO(new ProvisionDTO(123L)); // Örnek müşteri numarası
        createAccountingDTO.setAgentCode("AG123");
        createAccountingDTO.setChannelCode("CH002");
        createAccountingDTO.setCurrency(new Currency("USD"));
        createAccountingDTO.setChannelTransactionId("TX456");

        createAccountingResultDTO.setContractNo("CON789");
        createAccountingResultDTO.setTotalPaymentAmount(BigDecimal.valueOf(500));
        createAccountingResultDTO.setAvailableDate(LocalDate.now().plusDays(1));
    }

    @Test
    public void testPrintReceipt_WithCardPayment() {
        // Arrange
        createAccountingDTO.setPaymentMethodType(EnumProvisionType.CARD);
        createAccountingDTO.setPaymentMethodDetailDTO(new CreditCardPaymentMethodDetailDTO("4111111111111111"));
        createAccountingDTO.setInstitutionAccountingInfoDTO(new InstitutionAccountingInfoDTO(EnumReceiptType.BILL.getCode()));

        ProvisionDetailDTO provisionDetailDTO = new ProvisionDetailDTO(1L, BigDecimal.valueOf(-200), false, false, LocalDate.now());
        createAccountingResultDTO.setPendingDetailList(Collections.singletonList(provisionDetailDTO));

        // Act
        receiptServiceImpl.printReceipt(createAccountingDTO, createAccountingResultDTO);

        // Assert
        verify(receiptApiService).printReceipt(argThat(requests -> {
            if (requests.size() != 2) return false;
            RequestApiReceiptDTO debitRequest = requests.get(0);
            RequestApiReceiptDTO creditRequest = requests.get(1);

            return areEqualRequestApiReceiptDTO(debitRequest, createExpectedDebitRequest())
                    && areEqualRequestApiReceiptDTO(creditRequest, createExpectedCreditRequest());
        }));
    }

    @Test
    public void testPrintReceipt_WithAccountPayment() {
        // Arrange
        createAccountingDTO.setPaymentMethodType(EnumProvisionType.ACCOUNT);
        createAccountingDTO.setPaymentMethodDetailDTO(new AccountPaymentMethodDetailDTO("987654321"));
        createAccountingDTO.setInstitutionAccountingInfoDTO(new InstitutionAccountingInfoDTO(EnumReceiptType.BILL.getCode()));

        ProvisionDetailDTO provisionDetailDTO = new ProvisionDetailDTO(1L, BigDecimal.valueOf(-200), false, false, LocalDate.now());
        createAccountingResultDTO.setPendingDetailList(Collections.singletonList(provisionDetailDTO));

        // Act
        receiptServiceImpl.printReceipt(createAccountingDTO, createAccountingResultDTO);

        // Assert
        verify(receiptApiService).printReceipt(argThat(requests -> {
            if (requests.size() != 1) return false;
            RequestApiReceiptDTO debitRequest = requests.get(0);

            return areEqualRequestApiReceiptDTO(debitRequest, createExpectedDebitRequestForAccount());
        }));
    }

    @Test
    public void testPrintReceipt_WithEmptyPendingDetailList() {
        // Arrange
        createAccountingDTO.setPaymentMethodType(EnumProvisionType.CARD);
        createAccountingDTO.setPaymentMethodDetailDTO(new CreditCardPaymentMethodDetailDTO("4111111111111111"));
        createAccountingDTO.setInstitutionAccountingInfoDTO(new InstitutionAccountingInfoDTO(EnumReceiptType.BILL.getCode()));
        createAccountingResultDTO.setPendingDetailList(Collections.emptyList());

        // Act
        receiptServiceImpl.printReceipt(createAccountingDTO, createAccountingResultDTO);

        // Assert
        verify(receiptApiService).printReceipt(argThat(requests -> {
            if (requests.size() != 1) return false;
            RequestApiReceiptDTO debitRequest = requests.get(0);

            return areEqualRequestApiReceiptDTO(debitRequest, createExpectedDebitRequestWithEmptyPendingDetails());
        }));
    }

    private RequestApiReceiptDTO createExpectedDebitRequest() {
        RequestApiReceiptDTO expected = new RequestApiReceiptDTO();
        expected.setAccount("4111111111111111    USD");
        expected.setBranchCode("BR001");
        expected.setClientNo(123);
        expected.setCtype(AccountingConstant.C_TYPE);
        expected.setDtype(AccountingConstant.D_TYPE);
        expected.setProcessCode(EnumReceiptType.BILL.getCode());
        expected.setProcessDate(LocalDate.now());
        expected.setProccessRelDate(DateUtils.findRelDayDiff(LocalDateTime.now()));
        expected.setValueDate(LocalDate.now());
        expected.setContratNo("CON789");
        expected.setAmount(BigDecimal.valueOf(-500));
        expected.setUserCode("AG123");
        expected.setChannel("CH002");
        expected.setDescription("Institution-SubscriberNo");
        expected.setReverse("N");
        expected.setInvoiceNoFlag(Boolean.FALSE);
        expected.setProcessID("TX456");
        expected.setBasilacak("Y");
        expected.setTransactionId("TX456");
        expected.setPendingTransactionIdList(Collections.singletonList(1L));
        expected.setReceiptDetailList(createReceiptDetailListForCard());

        return expected;
    }

    private RequestApiReceiptDTO createExpectedCreditRequest() {
        RequestApiReceiptDTO expected = new RequestApiReceiptDTO();
        expected.setTransactionId("TX456");
        expected.setAccount("InstitutionAccountNo    USD");
        expected.setBranchCode("BR001");
        expected.setClientNo(123);
        expected.setCtype(AccountingConstant.C_TYPE);
        expected.setDtype(AccountingConstant.D_TYPE);
        expected.setProcessCode(AccountingConstant.DKNTSABIT);
        expected.setProccessRelDate(DateUtils.findRelDayDiff(LocalDateTime.now()));
        expected.setContratNo("CON789");
        expected.setEffectiveDate((int) DateUtils.findReldayDiff(LocalDate.now().plusDays(1)));
        expected.setValueDate(LocalDate.now().plusDays(1));
        expected.setProcessDate(LocalDate.now());
        expected.setAmount(BigDecimal.valueOf(500));
        expected.setUserCode("AG123");
        expected.setChannel("CH002");
        expected.setDescription("Institution-SubscriberNo");
        expected.setReverse("N");
        expected.setInvoiceNoFlag(false);
        expected.setProcessID("TX456");
        expected.setBasilacak("Y");
        expected.setReceiptDetailList(createCreditReceiptDetailList());

        return expected;
    }

    private RequestApiReceiptDTO createExpectedDebitRequestForAccount() {
        RequestApiReceiptDTO expected = new RequestApiReceiptDTO();
        expected.setAccount("987654321    USD");
        expected.setBranchCode("BR001");
        expected.setClientNo(123);
        expected.setCtype(AccountingConstant.C_TYPE);
        expected.setDtype(AccountingConstant.D_TYPE);
        expected.setProcessCode(EnumReceiptType.BILL.getCode());
        expected.setProcessDate(LocalDate.now());
        expected.setProccessRelDate(DateUtils.findRelDayDiff(LocalDateTime.now()));
        expected.setValueDate(LocalDate.now());
        expected.setContratNo("CON789");
        expected.setAmount(BigDecimal.valueOf(-500));
        expected.setUserCode("AG123");
        expected.setChannel("CH002");
        expected.setDescription("Institution-SubscriberNo");
        expected.setReverse("N");
        expected.setInvoiceNoFlag(Boolean.FALSE);
        expected.setProcessID("TX456");
        expected.setBasilacak("Y");
        expected.setTransactionId("TX456");
        expected.setPendingTransactionIdList(Collections.singletonList(1L));
        expected.setReceiptDetailList(createReceiptDetailListForAccount());

        return expected;
    }

    private RequestApiReceiptDTO createExpectedDebitRequestWithEmptyPendingDetails() {
        RequestApiReceiptDTO expected = new RequestApiReceiptDTO();
        expected.setAccount("4111111111111111    USD");
        expected.setBranchCode("BR001");
        expected.setClientNo(123);
        expected.setCtype(AccountingConstant.C_TYPE);
        expected.setDtype(AccountingConstant.D_TYPE);
        expected.setProcessCode(EnumReceiptType.BILL.getCode());
        expected.setProcessDate(LocalDate.now());
        expected.setProccessRelDate(DateUtils.findRelDayDiff(LocalDateTime.now()));
        expected.setValueDate(LocalDate.now());
        expected.setContratNo("CON789");
        expected.setAmount(BigDecimal.valueOf(500));
        expected.setUserCode("AG123");
        expected.setChannel("CH002");
        expected.setDescription("Institution-SubscriberNo");
        expected.setReverse("N");
        expected.setInvoiceNoFlag(Boolean.FALSE);
        expected.setProcessID("TX456");
        expected.setBasilacak("Y");
        expected.setTransactionId("TX456");
        expected.setPendingTransactionIdList(Collections.emptyList());
        expected.setReceiptDetailList(Collections.emptyList());

        return expected;
    }

    private List<ReceiptDetailDTO> createReceiptDetailListForCard() {
        ReceiptDetailDTO detail = new ReceiptDetailDTO();
        detail.setProvisionDetailId(1L);
        detail.setAmount(BigDecimal.valueOf(-200));
        detail.setPending(false);
        detail.setAccountNumber("4111111111111111");
        detail.setProvisionDate(LocalDate.now());
        return Collections.singletonList(detail);
    }

    private List<ReceiptDetailDTO> createCreditReceiptDetailList() {
        ReceiptDetailDTO detail = new ReceiptDetailDTO();
        detail.setProvisionDetailId(1L);
        detail.setAmount(BigDecimal.valueOf(500));
        detail.setPending(false);
        detail.setAccountNumber("InstitutionAccountNo");
        detail.setProvisionDate(LocalDate.now().plusDays(1));
        return Collections.singletonList(detail);
    }

    private List<ReceiptDetailDTO> createReceiptDetailListForAccount() {
        ReceiptDetailDTO detail = new ReceiptDetailDTO();
        detail.setProvisionDetailId(1L);
        detail.setAmount(BigDecimal.valueOf(-200));
        detail.setPending(false);
        detail.setAccountNumber("987654321");
        detail.setProvisionDate(LocalDate.now());
        return Collections.singletonList(detail);
    }

    private boolean areEqualRequestApiReceiptDTO(RequestApiReceiptDTO actual, RequestApiReceiptDTO expected) {
        return actual.getAccount().equals(expected.getAccount()) &&
               actual.getCreditCardNo().equals(expected.getCreditCardNo()) &&
               actual.getBranchCode().equals(expected.getBranchCode()) &&
               actual.getAmount().equals(expected.getAmount()) &&
               actual.getTransactionId().equals(expected.getTransactionId());
    }
}
