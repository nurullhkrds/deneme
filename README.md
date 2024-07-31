package com.ykb.payments.bill.transaction.accounting.receipt;

import com.ykb.payments.bill.common.enums.EnumCurrencyCode;
import com.ykb.payments.bill.transaction.accounting.dto.CreateAccountingDTO;
import com.ykb.payments.bill.transaction.accounting.dto.CreateAccountingResultDTO;
import com.ykb.payments.bill.transaction.external.corebanking.commission.model.response.ResponseCommissionInformation;
import com.ykb.payments.bill.transaction.external.corebanking.receipt.model.request.RequestApiReceiptDTO;
import com.ykb.payments.bill.transaction.external.corebanking.receipt.service.ReceiptApiService;
import com.ykb.payments.bill.transaction.institution.dto.*;
import com.ykb.payments.bill.transaction.institution.enums.EnumPaymentMethod;
import com.ykb.payments.bill.transaction.payment.enums.EnumProvisionStatus;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import java.math.BigDecimal;
import java.util.ArrayList;
import java.util.List;

import static org.junit.jupiter.api.Assertions.assertNotNull;
import static org.mockito.ArgumentMatchers.anyList;
import static org.mockito.Mockito.*;

class ReceiptServiceImplTest {

    @InjectMocks
    private ReceiptServiceImpl receiptService;

    @Mock
    private ReceiptApiService receiptApiService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testPrintReceiptIsValidRequest() {
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        ProvisionDTO provisionDTO = new ProvisionDTO();
        provisionDTO.setId(123L);
        provisionDTO.setCustomerNo(123L);
        provisionDTO.setStatus(EnumProvisionStatus.PAID);

        ResponseCommissionInformation responseCommissionInformation = new ResponseCommissionInformation();
        responseCommissionInformation.setInquiryId("123");
        responseCommissionInformation.setTotalCommissionLocalCurrencyAmount(BigDecimal.TEN);
        responseCommissionInformation.setTotalCommissionTaxLocalCurrencyAmount(BigDecimal.TEN);

        ProductDTO productDTO = new ProductDTO();
        productDTO.setCode("123");
        productDTO.setName("product");

        InstitutionDTO institutionDTO = new InstitutionDTO();
        institutionDTO.setId(123L);
        institutionDTO.setInstitutionCode("123");
        institutionDTO.setProduct(productDTO);

        InstitutionDebtTypeDTO institutionDebtTypeDTO = new InstitutionDebtTypeDTO();
        institutionDebtTypeDTO.setId(123L);
        institutionDebtTypeDTO.setInstitution(institutionDTO);

        InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO = new InstitutionChannelPymMethodDTO();
        institutionChannelPymMethodDTO.setId(123L);

        InstitutionChnnlPymMthdAccDTO institutionChnnlPymMthdAccDTO = new InstitutionChnnlPymMthdAccDTO();
        institutionChnnlPymMthdAccDTO.setId(123L);
        institutionChnnlPymMthdAccDTO.setInstitutionAccountNo("123");

        InstitutionChnnlPymMthdPscDTO institutionChnnlPymMthdPscDTO = new InstitutionChnnlPymMthdPscDTO();
        institutionChnnlPymMthdPscDTO.setId(123L);
        institutionChnnlPymMthdPscDTO.setInstitutionChannelPymMethod(institutionChannelPymMethodDTO);

        InstitutionAccountingInfoDTO institutionAccountingInfoDTO = new InstitutionAccountingInfoDTO();
        institutionAccountingInfoDTO.setId(123L);
        institutionAccountingInfoDTO.setReceiptCode("123");
        institutionAccountingInfoDTO.setInstitution(institutionDTO);

        createAccountingDTO.setProvisionDTO(provisionDTO);
        createAccountingDTO.setInstitutionAccountingInfoDTO(institutionAccountingInfoDTO);
        createAccountingDTO.setInstitutionChnnlPymMthdPscDTO(institutionChnnlPymMthdPscDTO);
        createAccountingDTO.setInstitutionChnnlPymMthdAccDTO(institutionChnnlPymMthdAccDTO);
        createAccountingDTO.setInstitutionChannelPymMethodDTO(institutionChannelPymMethodDTO);
        createAccountingDTO.setInstitutionDebtType(institutionDebtTypeDTO);
        createAccountingDTO.setInstitution(institutionDTO);
        createAccountingDTO.setResponseCommissionInformation(responseCommissionInformation);
        createAccountingDTO.setPaymentAmount(BigDecimal.TEN);
        createAccountingDTO.setCurrency(EnumCurrencyCode.DOLAR);
        createAccountingDTO.setPaymentMethodType(EnumPaymentMethod.ACCOUNT);
        createAccountingDTO.setChannelSessionId("123");
        createAccountingDTO.setChannelTransactionId("123");
        createAccountingDTO.setChannelCode("123");
        createAccountingDTO.setAgentCode("123");
        createAccountingDTO.setBranchCode("123");
        createAccountingDTO.setDummyMerchant(true);
        createAccountingDTO.setMerchantNo("123");

        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();

        receiptService.printReceipt(createAccountingDTO, createAccountingResultDTO);

        // Verify the call to receiptApiService.printReceipt
        verify(receiptApiService, times(1)).printReceipt(anyList());
    }

    @Test
    void testPrintReceipt_withDebitAndCredit() {
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        createAccountingDTO.setPaymentMethodType(EnumPaymentMethod.ACCOUNT);
        createAccountingDTO.setDummyMerchant(false);

        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();

        receiptService.printReceipt(createAccountingDTO, createAccountingResultDTO);

        // Verify the call to receiptApiService.printReceipt
        verify(receiptApiService, times(1)).printReceipt(argThat(receipts -> receipts.size() == 2));
    }

    @Test
    void testPrintReceipt_withOnlyDebit() {
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        createAccountingDTO.setPaymentMethodType(EnumPaymentMethod.CARD);
        createAccountingDTO.setDummyMerchant(true);

        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();

        receiptService.printReceipt(createAccountingDTO, createAccountingResultDTO);

        // Verify the call to receiptApiService.printReceipt
        verify(receiptApiService, times(1)).printReceipt(argThat(receipts -> receipts.size() == 1));
    }

    @Test
    void testReceiptDetailsPreparation() {
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        createAccountingDTO.setInstitutionAccountingInfoDTO(new InstitutionAccountingInfoDTO());
        createAccountingDTO.getInstitutionAccountingInfoDTO().setReceiptCode("SOME_CODE");

        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();

        receiptService.printReceipt(createAccountingDTO, createAccountingResultDTO);

        // Verify that the receipt details are prepared correctly
        verify(receiptApiService, times(1)).printReceipt(argThat(receipts -> {
            for (RequestApiReceiptDTO receipt : receipts) {
                assertNotNull(receipt.getReceiptDetailList());
                // Add more assertions based on expected details
            }
            return true;
        }));
    }

    @Test
    void testDescriptionPreparation() {
        CreateAccountingDTO createAccountingDTO = new CreateAccountingDTO();
        InstitutionDTO institutionDTO = new InstitutionDTO();
        institutionDTO.setName("Test Institution");
        createAccountingDTO.setInstitution(institutionDTO);
        ProvisionDTO provisionDTO = new ProvisionDTO();
        provisionDTO.setSubscriberNo("12345");
        createAccountingDTO.setProvisionDTO(provisionDTO);

        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();

        receiptService.printReceipt(createAccountingDTO, createAccountingResultDTO);

        // Verify the description
        verify(receiptApiService, times(1)).printReceipt(argThat(receipts -> {
            for (RequestApiReceiptDTO receipt : receipts) {
                assertEquals("Test Institution-12345", receipt.getDescription());
            }
            return true;
        }));
    }
}
