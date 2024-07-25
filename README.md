org.mockito.exceptions.misusing.WrongTypeOfReturnValue: 
Integer cannot be returned by getContractNumber()
getContractNumber() should return Long
***
If you're unsure why you're getting above error read on.
Due to the nature of the syntax above problem might occur because:
1. This exception *might* occur in wrongly written multi-threaded tests.
   Please refer to Mockito FAQ on limitations of concurrency testing.
2. A spy is stubbed using when(spy.foo()).then() syntax. It is safer to stub spies - 
   - with doReturn|Throw() family of methods. More in javadocs for Mockito.spy() method.   @Test
    void testDoAccounting_BlockDayCount() throws BusinessException, ServiceCallException {
        createAccountingDTO.setDummyMerchant(false);

        InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO = createAccountingDTO.getInstitutionChannelPymMethodDTO();
        institutionChannelPymMethodDTO.setBlockDayStrategyCode(EnumBlockDayStrategyCode.CHANNEL);
        when(institutionChannelPymMethodDTO.getBlockDayCount()).thenReturn(10);

        CardProvisionResponse cardProvisionResponse = new CardProvisionResponse();
        cardProvisionResponse.setGuid("123456");
        when(cardProvisionService.doProvision(any(CardProvisionRequest.class)))
                .thenReturn(cardProvisionResponse);
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
