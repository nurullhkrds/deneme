@Test
void testDoAccounting_CardProvisionGuidNull() {
    createAccountingDTO.setDummyMerchant(true);
    CardProvisionResponse response = new CardProvisionResponse();
    response.setGuid(null);
    when(cardProvisionService.doProvision(any(CardProvisionRequest.class))).thenReturn(response);

    CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);

    assertFalse(result.isSuccess());
    assertEquals(EnumBillResult.BILL_CREDIT_CARD_PROVISION_ERROR, result.getError());
}

@Test
void testDoAccounting_CardProvisionThrowsException() {
    createAccountingDTO.setDummyMerchant(true);
    when(cardProvisionService.doProvision(any(CardProvisionRequest.class))).thenThrow(new RuntimeException());

    CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);

    assertFalse(result.isSuccess());
    assertEquals(EnumBillResult.BILL_CREDIT_CARD_PROVISION_ERROR, result.getError());
}

@Test
void testDoAccounting_GLAccountingThrowsException() {
    createAccountingDTO.setDummyMerchant(true);
    when(cardProvisionService.doProvision(any(CardProvisionRequest.class))).thenReturn(new CardProvisionResponse());
    when(provisionNextService.makeProvision(any(MakeProvisionRequest.class))).thenThrow(new RuntimeException());

    CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);

    assertFalse(result.isSuccess());
    assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, result.getError());
}

@Test
void testHandleException() {
    CreateAccountingResultDTO resultDTO = new CreateAccountingResultDTO();
    cardProvisionServiceImpl.handleException(12345L, resultDTO);

    assertFalse(resultDTO.isSuccess());
    assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, resultDTO.getError());
}

@Test
void testGetBlockDayCount() {
    InstitutionChannelPymMethodDTO methodDTO = new InstitutionChannelPymMethodDTO();
    InstitutionChnnlPymMthdPscDTO pscDTO = new InstitutionChnnlPymMthdPscDTO();

    methodDTO.setBlockDayStrategyCode(EnumBlockDayStrategyCode.DAILY);
    when(pscDTO.getBlockDayCount(anyInt())).thenReturn(5);
    int blockDayCount = cardProvisionServiceImpl.getBlockDayCount(methodDTO, pscDTO);
    assertEquals(5, blockDayCount);

    methodDTO.setBlockDayStrategyCode(EnumBlockDayStrategyCode.CHANNEL);
    when(methodDTO.getBlockDayCount()).thenReturn(10);
    blockDayCount = cardProvisionServiceImpl.getBlockDayCount(methodDTO, pscDTO);
    assertEquals(10, blockDayCount);

    methodDTO.setBlockDayStrategyCode(EnumBlockDayStrategyCode.NO_VALOR);
    blockDayCount = cardProvisionServiceImpl.getBlockDayCount(methodDTO, pscDTO);
    assertEquals(0, blockDayCount);
}
