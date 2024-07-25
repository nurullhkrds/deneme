@Test
void testDoAccounting_GLAccountingFailure() throws BusinessException, ServiceCallException {
    createAccountingDTO.setDummyMerchant(true);
    CardProvisionResponse cardProvisionResponse = new CardProvisionResponse();
    cardProvisionResponse.setGuid("123456");
    when(cardProvisionService.doProvision(any(CardProvisionRequest.class)))
            .thenReturn(cardProvisionResponse);

    doThrow(new RuntimeException("GL Accounting Failed")).when(provisionNextService).makeProvision(any(MakeProvisionRequest.class));

    try {
        CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);
        assertFalse(result.isSuccess());
        assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, result.getError());
    } catch (RuntimeException e) {
        if (e.getCause() != null) {
            if (e.getCause() instanceof ServiceCallException) {
                Long errorCode = ((ServiceCallException) e.getCause()).getErrorCode();
                assertNotNull(errorCode);
            } else {
                fail("Unexpected exception cause: " + e.getCause().getClass().getName(), e);
            }
        } else {
            fail("Unexpected exception with null cause: " + e.getMessage(), e);
        }
    }
}
