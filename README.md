shouldHandleFailureIfMakeProvisionFails unitinde "java.lang.NullPointerException: Cannot invoke "com.ykb.payments.bill.transaction.institution.dto.InstitutionChnnlPymMthdAccDTO.getInstitutionAccountNo()" because the return value of "com.ykb.payments.bill.transaction.accounting.dto.CreateAccountingDTO.getInstitutionChnnlPymMthdAccDTO()" is null


" 

shouldHandleErrorIfContractNoIsNull unitinnde java.lang.NullPointerException: Cannot invoke "com.ykb.payments.bill.transaction.institution.dto.InstitutionChnnlPymMthdAccDTO.getInstitutionAccountNo()" because the return value of "com.ykb.payments.bill.transaction.accounting.dto.CreateAccountingDTO.getInstitutionChnnlPymMthdAccDTO()" is null


" 

shouldHandleExceptionAndReturnError unitinde "
org.mockito.exceptions.base.MockitoException: 
Checked exception is invalid for this method!
Invalid: com.ykb.architecture.micro.error.exception.ServiceCallException"


@Test
    void shouldHandleFailureIfMakeProvisionFails() throws ServiceCallException {
        // Arrange
        CreateAccountingDTO createAccountingDTO = createSampleDTO();

        // Simulate makeProvision returning null contractNo
        MakeProvisionResponse mockResponse = new MakeProvisionResponse();
        mockResponse.setSuccess(true);
        mockResponse.setContractNo(null);

        when(provisionNextService.makeProvision(any())).thenReturn(mockResponse);

        // Act
        CreateAccountingResultDTO resultDTO = accountProvisionService.doAccounting(createAccountingDTO);

        // Assert
        assertFalse(resultDTO.isSuccess());
        assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, resultDTO.getError());

        verify(provisionNextService, times(1)).makeProvision(any());
        verifyNoMoreInteractions(provisionNextService);
    }

    @Test
    void shouldHandleErrorIfContractNoIsNull() throws ServiceCallException {
        // Arrange
        CreateAccountingDTO createAccountingDTO = createSampleDTO();

        // Simulate makeProvision returning false success and null contractNo
        MakeProvisionResponse mockResponse = new MakeProvisionResponse();
        mockResponse.setSuccess(false);
        mockResponse.setContractNo(null);

        when(provisionNextService.makeProvision(any())).thenReturn(mockResponse);

        // Act
        CreateAccountingResultDTO resultDTO = accountProvisionService.doAccounting(createAccountingDTO);

        // Assert
        assertFalse(resultDTO.isSuccess());
        assertNotNull(resultDTO.getError());

        verify(provisionNextService, times(1)).makeProvision(any());
        verifyNoMoreInteractions(provisionNextService);
    }

    @Test
    void shouldHandleExceptionAndReturnError() throws ServiceCallException {
        // Arrange
        CreateAccountingDTO createAccountingDTO = createSampleDTO();

        // Simulate makeProvision throwing a RuntimeException
        when(provisionNextService.makeProvision(any())).thenThrow(new RuntimeException());

        // Act
        CreateAccountingResultDTO resultDTO = accountProvisionService.doAccounting(createAccountingDTO);

        // Assert
        assertFalse(resultDTO.isSuccess());
        assertNotNull(resultDTO.getError());

        verify(provisionNextService, times(1)).makeProvision(any());
        verifyNoMoreInteractions(provisionNextService);
    }

    @Test
    void shouldPrepareProvisionRequest() {
        // Arrange
        CreateAccountingDTO createAccountingDTO = createSampleDTO();
        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();

        // Act
        MakeProvisionRequest request = accountProvisionService.prepareProvisionRequest(createAccountingDTO, createAccountingResultDTO);

        // Assert
        assertNotNull(request);
        assertEquals(createAccountingDTO.getChannelTransactionId(), request.getTransactionId());
        assertEquals(createAccountingDTO.getInstitutionChannelPymMethodDTO().getAccountingTemplateCode(), request.getProvisionCode());
        assertEquals(createAccountingDTO.getChannelCode(), request.getChannelCode());
        assertEquals(createAccountingDTO.getAgentCode(), request.getUserCode());
        assertEquals(createAccountingDTO.getBranchCode(), request.getOperationalBranchCode());

        List<MakeProvisionInnerDTO> innerList = request.getMakeProvisionInnerList();
        assertNotNull(innerList);
        assertEquals(2, innerList.size()); // Assuming there are 2 transactions (debit and credit) in createSampleDTO()

        MakeProvisionInnerDTO debitTransaction = innerList.get(0);
        assertEquals(createAccountingDTO.getPaymentMethodDetailDTO().getAccountNo(), debitTransaction.getAccountNo());
        assertEquals(createAccountingDTO.getCurrency().getValue(), debitTransaction.getCurrency());
        assertEquals(createAccountingDTO.getPaymentAmount().negate(), debitTransaction.getAmount());
        assertEquals("Fatura Ödemesi", debitTransaction.getDescription());
        assertEquals(createAccountingDTO.getInstitutionChannelPymMethodDTO().getAccountingTemplateCode(), debitTransaction.getProvisionCode());
        assertEquals(createAccountingDTO.getProvisionDTO().getCustomerNo().intValue(), debitTransaction.getClientNo());

        MakeProvisionInnerDTO creditTransaction = innerList.get(1);
        assertEquals(createAccountingDTO.getInstitutionChnnlPymMthdAccDTO().getInstitutionAccountNo(), creditTransaction.getAccountNo());
        assertEquals(createAccountingDTO.getCurrency().getValue(), creditTransaction.getCurrency());
        assertEquals(createAccountingDTO.getPaymentAmount(), creditTransaction.getAmount());
        assertEquals("Fatura Ödemesi", creditTransaction.getDescription());
        assertEquals(createAccountingDTO.getInstitutionChannelPymMethodDTO().getAccountingTemplateCode(), creditTransaction.getProvisionCode());
        assertEquals(createAccountingDTO.getInstitution().getCustomerNo().intValue(), creditTransaction.getClientNo());

        // Other assertions for dates and commission handling if needed
    }

    // Helper methods for creating dummy data

    private CreateAccountingDTO createSampleDTO() {
        CreateAccountingDTO dto = new CreateAccountingDTO();
        dto.setChannelTransactionId("123");

        // Create ProvisionDTO if it's null
        if (dto.getProvisionDTO() == null) {
            dto.setProvisionDTO(new ProvisionDTO());
        }

        dto.getProvisionDTO().setCustomerNo("1234567890");  // Or set any necessary fields

        // Create InstitutionChannelPymMethodDTO if it's null
        if (dto.getInstitutionChannelPymMethodDTO() == null) {
            dto.setInstitutionChannelPymMethodDTO(new InstitutionChannelPymMethodDTO());
        }

        dto.getInstitutionChannelPymMethodDTO().setAccountingTemplateCode("template");
        dto.setChannelCode("channel");
        dto.setAgentCode("agent");
        dto.setBranchCode("branch");
        dto.setCurrency(EnumCurrencyCode.DOLAR);
        dto.setPaymentAmount(BigDecimal.TEN);

        AccountPaymentMethodDetailDTO paymentMethodDetailDTO = new AccountPaymentMethodDetailDTO();
        paymentMethodDetailDTO.setAccountNo("1234567890");
        dto.setPaymentMethodDetailDTO(paymentMethodDetailDTO);

        return dto;
    }
