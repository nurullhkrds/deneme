package com.example.service;

import com.example.dto.*;
import com.example.exception.ServiceCallException;
import com.example.integration.SwtSwitchIntegrationService;
import com.example.model.*;
import com.example.util.*;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import java.math.BigDecimal;
import java.time.LocalDate;
import java.util.ArrayList;
import java.util.List;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.*;

class CardProvisionServiceImplTest {

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

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testGetProvisionType() {
        assertEquals(EnumProvisionType.CARD, cardProvisionServiceImpl.getProvisionType());
    }

    @Test
    void testDoAccountingSuccessRealMerchant() {
        CreateAccountingDTO dto = new CreateAccountingDTO();
        dto.setDummyMerchant(false);
        dto.setPaymentAmount(BigDecimal.valueOf(100));
        dto.setCurrency(CurrencyType.TL);
        dto.setChannelCode("channelCode");
        dto.setChannelSessionId("sessionId");
        dto.setBranchCode("branchCode");
        dto.setMerchantNo("merchantNo");
        dto.setInstitution(new InstitutionDTO("Institution", 1L, new ProductDTO("Product")));
        dto.setProvisionDTO(new ProvisionDTO(1L));
        dto.setInstitutionChannelPymMethodDTO(new InstitutionChannelPymMethodDTO("templateCode", EnumBlockDayStrategyCode.NO_VALOR));
        dto.setInstitutionChnnlPymMthdAccDTO(new InstitutionChnnlPymMthdAccDTO("accountNo"));
        dto.setInstitutionChnnlPymMthdPscDTO(new InstitutionChnnlPymMthdPscDTO(1));

        CreateAccountingResultDTO cardProvisionResultDTO = new CreateAccountingResultDTO();
        cardProvisionResultDTO.setSuccess(true);
        cardProvisionResultDTO.setProvisionRequestId("requestId");

        when(accountingUtilServiceImpl.getContractNumber()).thenReturn(12345L);
        when(cardProvisionService.doProvision(any(CardProvisionRequest.class)))
            .thenReturn(new CardProvisionResponse("guid"));

        CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(dto);
        assertTrue(result.isSuccess());
        assertEquals(12345L, result.getContractNo());
        assertEquals("requestId", result.getProvisionRequestId());
    }

    @Test
    void testDoAccountingSuccessDummyMerchant() {
        CreateAccountingDTO dto = new CreateAccountingDTO();
        dto.setDummyMerchant(true);
        dto.setPaymentAmount(BigDecimal.valueOf(100));
        dto.setCurrency(CurrencyType.TL);
        dto.setChannelCode("channelCode");
        dto.setChannelSessionId("sessionId");
        dto.setBranchCode("branchCode");
        dto.setMerchantNo("merchantNo");
        dto.setInstitution(new InstitutionDTO("Institution", 1L, new ProductDTO("Product")));
        dto.setProvisionDTO(new ProvisionDTO(1L));
        dto.setInstitutionChannelPymMethodDTO(new InstitutionChannelPymMethodDTO("templateCode", EnumBlockDayStrategyCode.NO_VALOR));
        dto.setInstitutionChnnlPymMthdAccDTO(new InstitutionChnnlPymMthdAccDTO("accountNo"));
        dto.setInstitutionChnnlPymMthdPscDTO(new InstitutionChnnlPymMthdPscDTO(1));

        when(cardProvisionService.doProvision(any(CardProvisionRequest.class)))
            .thenReturn(new CardProvisionResponse("guid"));

        when(provisionNextService.makeProvision(any(MakeProvisionRequest.class)))
            .thenReturn(new MakeProvisionResponse(true, "contractNo", new ArrayList<>()));

        CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(dto);
        assertTrue(result.isSuccess());
        assertEquals("contractNo", result.getContractNo());
    }

    @Test
    void testDoAccountingFailure() {
        CreateAccountingDTO dto = new CreateAccountingDTO();
        dto.setDummyMerchant(false);

        when(cardProvisionService.doProvision(any(CardProvisionRequest.class)))
            .thenThrow(new RuntimeException("Provision error"));

        CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(dto);
        assertFalse(result.isSuccess());
        assertEquals(EnumBillResult.BILL_CREDIT_CARD_PROVISION_ERROR, result.getError());
    }

    @Test
    void testDoCardProvision() {
        CreateAccountingDTO dto = new CreateAccountingDTO();
        dto.setChannelCode("channelCode");
        dto.setPaymentAmount(BigDecimal.valueOf(100));
        dto.setCurrency(CurrencyType.TL);
        dto.setDummyMerchant(false);

        when(cardProvisionService.doProvision(any(CardProvisionRequest.class)))
            .thenReturn(new CardProvisionResponse("guid"));

        CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(dto);
        assertTrue(result.isSuccess());
        assertEquals("guid", result.getProvisionRequestId());
    }

    @Test
    void testDoGLAccounting() {
        CreateAccountingDTO dto = new CreateAccountingDTO();
        dto.setPaymentAmount(BigDecimal.valueOf(100));
        dto.setCurrency(CurrencyType.TL);
        dto.setChannelTransactionId("transactionId");
        dto.setInstitutionChannelPymMethodDTO(new InstitutionChannelPymMethodDTO("templateCode", EnumBlockDayStrategyCode.NO_VALOR));
        dto.setInstitutionChnnlPymMthdAccDTO(new InstitutionChnnlPymMthdAccDTO("accountNo"));
        dto.setInstitution(new InstitutionDTO("Institution", 1L, new ProductDTO("Product")));
        dto.setProvisionDTO(new ProvisionDTO(1L));

        when(provisionNextService.makeProvision(any(MakeProvisionRequest.class)))
            .thenReturn(new MakeProvisionResponse(true, "contractNo", new ArrayList<>()));

        CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(dto);
        assertTrue(result.isSuccess());
        assertEquals("contractNo", result.getContractNo());
    }

    @Test
    void testHandleException() {
        CreateAccountingResultDTO resultDTO = new CreateAccountingResultDTO();
        cardProvisionServiceImpl.handleException(123L, resultDTO);
        assertFalse(resultDTO.isSuccess());
        assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, resultDTO.getError());
    }
}
