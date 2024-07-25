 @Test
    void testDoAccounting_SuccessfulDummyMerchant() {
        createAccountingDTO.setDummyMerchant(true);
        CardProvisionResponse cardProvisionResponse = new CardProvisionResponse();
        cardProvisionResponse.setGuid("123456");
        when(cardProvisionService.doProvision(any(CardProvisionRequest.class)))
                .thenReturn(cardProvisionResponse);

        try {
            CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);
            assertTrue(result.isSuccess());
            assertNotNull(result.getOceanTransactionId());
        } catch (Exception e) {
            if (e.getCause() != null && e.getCause().getClass().equals(ServiceCallException.class)) {
                Long errorCode = ((ServiceCallException) e.getCause()).getErrorCode();
                assertNotNull(errorCode);
            } else {
                fail("Unexpected exception");
            }
        }
    }
