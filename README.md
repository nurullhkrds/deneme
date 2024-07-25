@Test
void testDoAccounting_CardProvisionHandleException() {
    createAccountingDTO.setDummyMerchant(true);
    when(cardProvisionService.doProvision(any(CardProvisionRequest.class)))
            .thenThrow(new RuntimeException("Provision Failed"));

    CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);

    assertFalse(result.isSuccess());
    assertEquals(EnumBillResult.BILL_CREDIT_CARD_PROVISION_ERROR, result.getError());
}

@Test
void testDoAccounting_GLAccountingHandleException() {
    createAccountingDTO.setDummyMerchant(true);
    when(cardProvisionService.doProvision(any(CardProvisionRequest.class)))
            .thenReturn(new CardProvisionResponse("guid123"));
    when(provisionNextService.makeProvision(any(MakeProvisionRequest.class)))
            .thenThrow(new RuntimeException("GL Accounting Failed"));

    CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);

    assertFalse(result.isSuccess());
    assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, result.getError());
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

    CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);

    assertTrue(result.isSuccess());
    verify(accountingDateUtil, times(1))
            .getAvailDate(any(EnumBlockDayType.class), anyInt());
}
