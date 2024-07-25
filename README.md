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
    } catch (Exception e) {
        if (e.getCause() != null) {
            if (e.getCause().getClass().equals(ServiceCallException.class)) {
                Long errorCode = ((ServiceCallException) e.getCause()).getErrorCode();
                assertNotNull(errorCode);
            } else {
                fail("Unexpected exception cause: " + e.getCause().getClass().getName(), e);
            }
        } else {
            fail("Unexpected exception: " + e.getMessage(), e);
        }
    }
}
