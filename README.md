@Test
    public void testGetBillPaymentExpense() throws Exception {
        GetBillPaymentExpenseRequestDTO requestDTO = new GetBillPaymentExpenseRequestDTO();
        requestDTO.setAgentCode("agentCode");
        requestDTO.setChannelCode("channelCode");
        requestDTO.setOperatingBranchCode("branchCode");

        GetBillPaymentExpenseResponseDTO responseDTO = new GetBillPaymentExpenseResponseDTO();
        // Populate responseDTO with expected data

        when(paymentFacade.getBillPaymentExpense(any(GetBillPaymentExpenseRequestDTO.class))).thenReturn(responseDTO);

        mockMvc.perform(get("/adkBillPayment/getBillPaymentExpense")
                .param("agentCode", requestDTO.getAgentCode())
                .param("channelCode", requestDTO.getChannelCode())
                .param("operatingBranchCode", requestDTO.getOperatingBranchCode())
                .header("x-trace-id", "traceId")
                .header("x-session-id", "sessionId")
                .contentType(MediaType.APPLICATION_JSON))
                .andExpect(status().isOk())
                .andExpect(content().contentType(MediaType.APPLICATION_JSON))
                .andExpect(jsonPath("$.field").value("expectedValue"));

        verify(paymentFacade, times(1)).getBillPaymentExpense(any(GetBillPaymentExpenseRequestDTO.class));
    }
