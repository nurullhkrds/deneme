@Test
    void testDoAccounting_PrepareProvisionRequest() {
        createAccountingDTO.setDummyMerchant(true);

        MakeProvisionResponse makeProvisionResponse = new MakeProvisionResponse();
        makeProvisionResponse.setSuccess(true);
        makeProvisionResponse.setContractNo(123456L);
        when(provisionNextService.makeProvision(any(MakeProvisionRequest.class)))
                .thenReturn(makeProvisionResponse);

        CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);

        assertFalse(result.isSuccess());

        ArgumentCaptor<MakeProvisionRequest> argumentCaptor = ArgumentCaptor.forClass(MakeProvisionRequest.class);
        verify(provisionNextService).makeProvision(argumentCaptor.capture());
        MakeProvisionRequest capturedRequest = argumentCaptor.getValue();

        assertEquals(createAccountingDTO.getChannelTransactionId(), capturedRequest.getTransactionId());
    }

Wanted but not invoked:
provisionNextService.makeProvision(
    <Capturing argument>
);
-> at com.ykb.payments.bill.transaction.accounting.provision.service.CardProvisionServiceImplTest.testDoAccounting_PrepareProvisionRequest(CardProvisionServiceImplTest.java:216)
Actually, there were zero interactions with this mock.

Wanted but not invoked:
provisionNextService.makeProvision(
    <Capturing argument>
);
-> at com.ykb.payments.bill.transaction.accounting.provision.service.CardProvisionServiceImplTest.testDoAccounting_PrepareProvisionRequest(CardProvisionServiceImplTest.java:216)
Actually, there were zero interactions with this mock.
