@Test
    @Transactional
    void notifyPaymentCancel_shouldReturnNotifyPaymentCancelResponse() throws MicroException {
        // Given
        NotifyPaymentCancelRequest request = new NotifyPaymentCancelRequest();
        NotifyPaymentCancelProcessInput processInput = new NotifyPaymentCancelProcessInput();
        NotifyPaymentCancelProcessOutput processOutput = new NotifyPaymentCancelProcessOutput();
        NotifyPaymentCancelResponse notifyPaymentCancelResponse = new NotifyPaymentCancelResponse();

        // Mocking the requestContext to return specific values
        when(requestContext.getChannelSessionId()).thenReturn("channelSessionId");
        when(requestContext.getChannelTransactionId()).thenReturn("channelTransactionId");

        // Mocking the processManager to return the processOutput
        when(processManager.executeProcess(any(NotifyPaymentCancelProcessInput.class)))
                .thenReturn(processOutput);

        // Mocking the paymentMapper to return the NotifyPaymentCancelResponse
        when(paymentMapper.toNotifyPaymentCancelResponse(processOutput))
                .thenReturn(notifyPaymentCancelResponse);

        // When
        NotifyPaymentCancelResponse result = paymentService.notifyPaymentCancel(request);

        // Then
        assertNotNull(result);
        assertEquals(notifyPaymentCancelResponse, result);
        verify(processManager).executeProcess(any(NotifyPaymentCancelProcessInput.class));
        verify(paymentMapper).toNotifyPaymentCancelResponse(processOutput);
    }
