package com.ykb.payments.bill.transaction.accounting.receipt;

import com.ykb.payments.bill.common.enums.EnumCurrencyCode;
import com.ykb.payments.bill.transaction.accounting.dto.CreateAccountingDTO;
import com.ykb.payments.bill.transaction.accounting.dto.CreateAccountingResultDTO;
import com.ykb.payments.bill.transaction.external.corebanking.commission.model.response.ResponseCommissionInformation;
import com.ykb.payments.bill.transaction.external.corebanking.receipt.model.request.RequestApiReceiptDTO;
import com.ykb.payments.bill.transaction.external.corebanking.receipt.service.ReceiptApiService;
import com.ykb.payments.bill.transaction.institution.dto.*;
import com.ykb.payments.bill.transaction.institution.enums.EnumPaymentMethod;
import com.ykb.payments.bill.transaction.payment.dto.AccountPaymentMethodDetailDTO;
import com.ykb.payments.bill.transaction.payment.dto.PaymentMethodDetailDTO;
import com.ykb.payments.bill.transaction.payment.dto.ProvisionDTO;
import com.ykb.payments.bill.transaction.payment.enums.EnumProvisionStatus;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import java.math.BigDecimal;


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

        AccountPaymentMethodDetailDTO paymentMethodDetailDTO =new AccountPaymentMethodDetailDTO();
        paymentMethodDetailDTO.setAccountNo("123");
        paymentMethodDetailDTO.setAccountBranchCode("123");

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
        createAccountingDTO.setPaymentMethodDetailDTO(paymentMethodDetailDTO);
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

    }

  
}
