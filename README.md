   @Test
    void doBillPayment_shouldReturnDoBillPaymentResponse_whenProcessManagerExecutesSuccessfully() throws MicroException {
        // Given
        DoBillPaymentRequest request = new DoBillPaymentRequest();
        BillPaymentProcessInput processInput = new BillPaymentProcessInput();
        BillPaymentProcessOutput processOutput = new BillPaymentProcessOutput();
        DoBillPaymentResponse expectedResponse = new DoBillPaymentResponse();

        processOutput.setBillId("12345");
        processOutput.setContractNo("contract123");
        expectedResponse.setBillId("12345");
        expectedResponse.setContractNumber("contract123");

        when(processExecutionMapper.toBillPaymentProcessInput(request)).thenReturn(processInput);
        when(requestContext.getChannelSessionId()).thenReturn("sessionId");
        when(requestContext.getChannelTransactionId()).thenReturn("transactionId");
        when(processManager.executeProcess(processInput)).thenReturn(processOutput);

        // When
        DoBillPaymentResponse actualResponse = paymentService.doBillPayment(request);

        // Then
        assertEquals(expectedResponse.getBillId(), actualResponse.getBillId());
        assertEquals(expectedResponse.getContractNumber(), actualResponse.getContractNumber());
        verify(processExecutionMapper).toBillPaymentProcessInput(request);
        verify(requestContext).getChannelSessionId();
        verify(requestContext).getChannelTransactionId();
        verify(processManager).executeProcess(processInput);
    }

    @Test
    void cancelBillPayment_shouldReturnCancelBillPaymentResponse_whenProcessManagerExecutesSuccessfully() throws MicroException {
        // Given
        CancelBillPaymentRequest request = new CancelBillPaymentRequest();
        BillPaymentReverseProcessInput reverseProcessInput = new BillPaymentReverseProcessInput();
        CancelBillPaymentResponse expectedResponse = new CancelBillPaymentResponse();

        when(processExecutionMapper.toBillPaymentReverseProcessInput(request)).thenReturn(reverseProcessInput);
        when(requestContext.getChannelSessionId()).thenReturn("sessionId");
        when(requestContext.getChannelTransactionId()).thenReturn("transactionId");

        // When
        CancelBillPaymentResponse actualResponse = paymentService.cancelBillPayment(request);

        // Then
        assertEquals(expectedResponse, actualResponse);
        verify(processExecutionMapper).toBillPaymentReverseProcessInput(request);
        verify(requestContext).getChannelSessionId();
        verify(requestContext).getChannelTransactionId();
        verify(processManager).executeProcess(reverseProcessInput);
    }
