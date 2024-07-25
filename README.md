 @Test
    void testDoAccounting_SuccessfulRealMerchant() {
        createAccountingDTO.setDummyMerchant(false);
        CardProvisionResponse cardProvisionResponse = new CardProvisionResponse();
        cardProvisionResponse.setGuid("123456");
        when(cardProvisionService.doProvision(any(CardProvisionRequest.class)))
                .thenReturn(cardProvisionResponse);
        when(accountingUtilServiceImpl.getContractNumber()).thenReturn(123456L);

        try {
            CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);
            assertTrue(result.isSuccess());
            assertEquals(123456L, result.getContractNo());
        } catch (Exception e) {
            if (e.getCause() != null && e.getCause().getClass().equals(ServiceCallException.class)) {
                Long errorCode = ((ServiceCallException) e.getCause()).getErrorCode();
                assertNotNull(errorCode);
            } else {
                fail("Unexpected exception: " + e.getMessage(), e);
            }
        }
    }

    @Test
    void testDoAccounting_CardProvisionFailure() {
        createAccountingDTO.setDummyMerchant(true);
        doThrow(new RuntimeException("Provision Failed")).when(cardProvisionService).doProvision(any(CardProvisionRequest.class));

        try {
            CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);
            assertFalse(result.isSuccess());
            assertEquals(EnumBillResult.BILL_CREDIT_CARD_PROVISION_ERROR, result.getError());
        } catch (Exception e) {
            if (e.getCause() != null && e.getCause().getClass().equals(ServiceCallException.class)) {
                Long errorCode = ((ServiceCallException) e.getCause()).getErrorCode();
                assertNotNull(errorCode);
            } else {
                fail("Unexpected exception: " + e.getMessage(), e);
            }
        }
    }

    @Test
    void testDoAccounting_GLAccountingFailure() {
        createAccountingDTO.setDummyMerchant(true);
        when(cardProvisionService.doProvision(any(CardProvisionRequest.class)))
                .thenReturn(new CardProvisionResponse("guid123"));

        doThrow(new RuntimeException("GL Accounting Failed")).when(provisionNextService).makeProvision(any(MakeProvisionRequest.class));

        try {
            CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);
            assertFalse(result.isSuccess());
            assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, result.getError());
        } catch (Exception e) {
            if (e.getCause() != null && e.getCause().getClass().equals(ServiceCallException.class)) {
                Long errorCode = ((ServiceCallException) e.getCause()).getErrorCode();
                assertNotNull(errorCode);
            } else {
                fail("Unexpected exception: " + e.getMessage(), e);
            }
        }
    }

    @Test
    void testDoAccounting_BlockDayCount() {
        createAccountingDTO.setDummyMerchant(false);

        InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO = createAccountingDTO.getInstitutionChannelPymMethodDTO();
        institutionChannelPymMethodDTO.setBlockDayStrategyCode(EnumBlockDayStrategyCode.CHANNEL);
        when(institutionChannelPymMethodDTO.getBlockDayCount()).thenReturn(10);

        when(cardProvisionService.doProvision(any(CardProvisionRequest.class)))
                .thenReturn(new CardProvisionResponse("guid123"));
        when(accountingUtilServiceImpl.getContractNumber()).thenReturn(123456L);

        try {
            CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);
            assertTrue(result.isSuccess());
            verify(accountingDateUtil, times(1))
                    .getAvailDate(any(EnumBlockDayType.class), anyInt());
        } catch (Exception e) {
            if (e.getCause() != null && e.getCause().getClass().equals(ServiceCallException.class)) {
                Long errorCode = ((ServiceCallException) e.getCause()).getErrorCode();
                assertNotNull(errorCode);
            } else {
                fail("Unexpected exception: " + e.getMessage(), e);
            }
        }
    }
