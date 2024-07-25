import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.when;
import static org.mockito.Mockito.doThrow;
import static org.mockito.Mockito.verify;
import static org.mockito.Mockito.times;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import static org.junit.jupiter.api.Assertions.*;

import java.math.BigDecimal;
import java.time.LocalDate;
import java.util.Arrays;
import java.util.Calendar;

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
        
        InstitutionDTO institutionDTO = new InstitutionDTO();
        institutionDTO.setName("Test Institution");
        ProductDTO productDTO = new ProductDTO();
        productDTO.setProductCampaignCode("ProductCampaignCode");
        institutionDTO.setProduct(productDTO);
        institutionDTO.setId(1L);
        institutionDTO.setCustomerNo(1001L);
        createAccountingDTO.setInstitution(institutionDTO);

        InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO = new InstitutionChannelPymMethodDTO();
        institutionChannelPymMethodDTO.setAccountingTemplateCode("TemplateCode");
        institutionChannelPymMethodDTO.setBlockDayStrategyCode(EnumBlockDayStrategyCode.NO_VALOR);
        createAccountingDTO.setInstitutionChannelPymMethodDTO(institutionChannelPymMethodDTO);

        InstitutionChnnlPymMthdAccDTO institutionChnnlPymMthdAccDTO = new InstitutionChnnlPymMthdAccDTO();
        institutionChnnlPymMthdAccDTO.setInstitutionAccountNo("AccountNo");
        createAccountingDTO.setInstitutionChnnlPymMthdAccDTO(institutionChnnlPymMthdAccDTO);

        createAccountingDTO.setCurrency(EnumCurrencyCode.USD);
        createAccountingDTO.setChannelCode("TestChannel");
        createAccountingDTO.setBranchCode("TestBranch");
        createAccountingDTO.setChannelSessionId("TestSessionId");
        createAccountingDTO.setMerchantNo("TestMerchantNo");
        createAccountingDTO.setPaymentAmount(BigDecimal.TEN);
        createAccountingDTO.setDummyMerchant(false);

        CreditCardPaymentMethodDetailDTO creditCardPaymentMethodDetailDTO = new CreditCardPaymentMethodDetailDTO();
        creditCardPaymentMethodDetailDTO.setCardNumber("4111111111111111");
        createAccountingDTO.setPaymentMethodDetailDTO(creditCardPaymentMethodDetailDTO);

        ProvisionDTO provisionDTO = new ProvisionDTO();
        provisionDTO.setCustomerNo(1001L);
        createAccountingDTO.setProvisionDTO(provisionDTO);

        ResponseCommissionInformation responseCommissionInformation = new ResponseCommissionInformation();
        responseCommissionInformation.setInquiryId("InquiryId");
        responseCommissionInformation.setTotalCommissionTaxLocalCurrencyAmount(BigDecimal.ONE);
        responseCommissionInformation.setTotalCommissionLocalCurrencyAmount(BigDecimal.ONE);
        createAccountingDTO.setResponseCommissionInformation(responseCommissionInformation);

        InstitutionChnnlPymMthdPscDTO institutionChnnlPymMthdPscDTO = new InstitutionChnnlPymMthdPscDTO();
        createAccountingDTO.setInstitutionChnnlPymMthdPscDTO(institutionChnnlPymMthdPscDTO);
    }

    @Test
    void testDoAccounting_SuccessfulDummyMerchant() {
        createAccountingDTO.setDummyMerchant(true);
        when(cardProvisionService.doProvision(any(CardProvisionRequest.class)))
                .thenReturn(new CardProvisionResponse("guid123"));

        CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);

        assertTrue(result.isSuccess());
        assertNotNull(result.getOceanTransactionId());
    }

    @Test
    void testDoAccounting_SuccessfulRealMerchant() {
        createAccountingDTO.setDummyMerchant(false);
        when(cardProvisionService.doProvision(any(CardProvisionRequest.class)))
                .thenReturn(new CardProvisionResponse("guid123"));
        when(accountingUtilServiceImpl.getContractNumber()).thenReturn(123456L);

        CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);

        assertTrue(result.isSuccess());
        assertEquals(123456L, result.getContractNo());
    }

    @Test
    void testDoAccounting_CardProvisionFailure() {
        createAccountingDTO.setDummyMerchant(true);
        doThrow(new RuntimeException()).when(cardProvisionService).doProvision(any(CardProvisionRequest.class));

        CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);

        assertFalse(result.isSuccess());
        assertEquals(EnumBillResult.BILL_CREDIT_CARD_PROVISION_ERROR, result.getError());
    }

    @Test
    void testDoAccounting_GLAccountingFailure() {
        createAccountingDTO.setDummyMerchant(true);
        when(cardProvisionService.doProvision(any(CardProvisionRequest.class)))
                .thenReturn(new CardProvisionResponse("guid123"));
        doThrow(new RuntimeException()).when(provisionNextService).makeProvision(any(MakeProvisionRequest.class));

        CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);

        assertFalse(result.isSuccess());
        assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, result.getError());
    }

    @Test
    void testHandleException() {
        CreateAccountingResultDTO resultDTO = new CreateAccountingResultDTO();
        cardProvisionServiceImpl.handleException(12345L, resultDTO);

        assertFalse(resultDTO.isSuccess());
        assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, resultDTO.getError());
    }

    @Test
    void testGetBlockDayCount() {
        InstitutionChannelPymMethodDTO methodDTO = new InstitutionChannelPymMethodDTO();
        InstitutionChnnlPymMthdPscDTO pscDTO = new InstitutionChnnlPymMthdPscDTO();

        methodDTO.setBlockDayStrategyCode(EnumBlockDayStrategyCode.DAILY);
        when(pscDTO.getBlockDayCount(anyInt())).thenReturn(5);
        int blockDayCount = cardProvisionServiceImpl.getBlockDayCount(methodDTO, pscDTO);
        assertEquals(5, blockDayCount);

        methodDTO.setBlockDayStrategyCode(EnumBlockDayStrategyCode.CHANNEL);
        when(methodDTO.getBlockDayCount()).thenReturn(10);
        blockDayCount = cardProvisionServiceImpl.getBlockDayCount(methodDTO, pscDTO);
        assertEquals(10, blockDayCount);

        methodDTO.setBlockDayStrategyCode(EnumBlockDayStrategyCode.NO_VALOR);
        blockDayCount = cardProvisionServiceImpl.getBlockDayCount(methodDTO, pscDTO);
        assertEquals(0, blockDayCount);
    }
}
