  @Test
    @Transactional
    void notifyPaymentCancel_shouldReturnNotifyPaymentCancelResponse() throws MicroException {
        // Given
        NotifyPaymentCancelRequest request = new NotifyPaymentCancelRequest();
        request.setAgentCode("agentCode");
        request.setOperatingBranchCode("branchCode");
        request.setChannelCode("channelCode");
        request.setProductCode("productCode");
        request.setInstitutionCode("institutionCode");
        request.setPaymentNotificationId("paymentNotificationId");

        NotifyPaymentCancelProcessInput processInput = new NotifyPaymentCancelProcessInput();
        processInput.setAgentCode(request.getAgentCode());
        processInput.setBranchCode(request.getOperatingBranchCode());
        processInput.setChannelCode(request.getChannelCode());
        processInput.setChannelSessionId(requestContext.getChannelSessionId());
        processInput.setChannelTransactionId(requestContext.getChannelTransactionId());
        processInput.setProductCode(request.getProductCode());
        processInput.setInstitutionCode(request.getInstitutionCode());
        processInput.setPaymentNotificationId(request.getPaymentNotificationId());

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
        verify(processManager).executeProcess(argThat(input -> {
            // Check if all fields of the input match
            assertEquals(processInput.getAgentCode(), input.getAgentCode());
            assertEquals(processInput.getBranchCode(), input.getBranchCode());
            assertEquals(processInput.getChannelCode(), input.getChannelCode());
            assertEquals(processInput.getChannelSessionId(), input.getChannelSessionId());
            assertEquals(processInput.getChannelTransactionId(), input.getChannelTransactionId());
            assertEquals(processInput.getProductCode(), input.getProductCode());
            assertEquals(processInput.getInstitutionCode(), input.getInstitutionCode());
            assertEquals(processInput.getPaymentNotificationId(), input.getPaymentNotificationId());
            return true;
        }));
        verify(paymentMapper).toNotifyPaymentCancelResponse(processOutput);
    }
