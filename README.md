@Test
void testDoAccounting_SuccessfulDummyMerchant() {
    createAccountingDTO.setDummyMerchant(true);

    MakeProvisionResponse makeProvisionResponse = new MakeProvisionResponse();
    makeProvisionResponse.setSuccess(true);
    makeProvisionResponse.setContractNo(123456L);
    lenient().when(provisionNextService.makeProvision(any(MakeProvisionRequest.class)))
            .thenReturn(makeProvisionResponse);

    CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);

    assertTrue(result.isSuccess());
    assertEquals(123456L, result.getContractNo());
}

@Test
void testDoAccounting_GLAccountingFailure() throws BusinessException, ServiceCallException {
    createAccountingDTO.setDummyMerchant(true);

    MakeProvisionResponse makeProvisionResponse = new MakeProvisionResponse();
    makeProvisionResponse.setSuccess(false);
    makeProvisionResponse.setErrorCode(100L);
    lenient().when(provisionNextService.makeProvision(any(MakeProvisionRequest.class)))
            .thenReturn(makeProvisionResponse);

    CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);

    assertFalse(result.isSuccess());
    assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, result.getError());
}
