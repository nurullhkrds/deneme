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
                .accept(MediaType.APPLICATION_JSON))
                .andExpect(status().isBadRequest()) // 400 durum kodunu bekliyoruz
                .andDo(result -> {
                    System.out.println("Response: " + result.getResponse().getContentAsString());
                    System.out.println("Status: " + result.getResponse().getStatus());
                });
    }
