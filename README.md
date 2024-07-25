  @Test
    void testDoAccounting_SuccessfulDummyMerchant() throws BusinessException, ServiceCallException {
        createAccountingDTO.setDummyMerchant(true);
        CardProvisionResponse cardProvisionResponse = new CardProvisionResponse();
        cardProvisionResponse.setGuid("123456");
        when(cardProvisionService.doProvision(any(CardProvisionRequest.class)))
                .thenReturn(cardProvisionResponse);

        CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);

        assertTrue(result.isSuccess());
        assertNotNull(result.getOceanTransactionId());
    }java.lang.NullPointerException: Cannot invoke "Object.getClass()" because the return value of "java.lang.Exception.getCause()" is null
