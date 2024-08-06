
    @Test
    void testDoGLAccountingSuccess() throws Exception {
        createAccountingDTO.setDummyMerchant(true);
        CreateAccountingResultDTO resultDTO = new CreateAccountingResultDTO();

        MakeProvisionResponse makeProvisionResponse = new MakeProvisionResponse();
        makeProvisionResponse.setSuccess(true);
        makeProvisionResponse.setContractNo(123456L);
        when(provisionNextService.makeProvision(any(MakeProvisionRequest.class)))
                .thenReturn(makeProvisionResponse);

        Method method = CardProvisionServiceImpl.class.getDeclaredMethod("doGLAccounting", CreateAccountingDTO.class, CreateAccountingResultDTO.class);
        method.setAccessible(true);
        CreateAccountingResultDTO result = (CreateAccountingResultDTO) method.invoke(cardProvisionServiceImpl, createAccountingDTO, resultDTO);

        assertTrue(result.isSuccess());
        assertEquals(123456L, result.getContractNo());
    }

    @Test
    void testPrepareProvisionRequest() throws Exception {
        createAccountingDTO.setDummyMerchant(true);
        CreateAccountingResultDTO resultDTO = new CreateAccountingResultDTO();

        Method method = CardProvisionServiceImpl.class.getDeclaredMethod("prepareProvisionRequest", CreateAccountingDTO.class, CreateAccountingResultDTO.class);
        method.setAccessible(true);
        MakeProvisionRequest request = (MakeProvisionRequest) method.invoke(cardProvisionServiceImpl, createAccountingDTO, resultDTO);

        assertNotNull(request);
        assertEquals(createAccountingDTO.getChannelTransactionId(), request.getTransactionId());
        assertEquals(createAccountingDTO.getInstitutionChannelPymMethodDTO().getAccountingTemplateCode(), request.getProvisionCode());
    }

    @Test
void testHandleException() throws Exception {
    CreateAccountingResultDTO resultDTO = new CreateAccountingResultDTO();

    Method method = CardProvisionServiceImpl.class.getDeclaredMethod("handleException", Long.class, CreateAccountingResultDTO.class);
    method.setAccessible(true);
    method.invoke(cardProvisionServiceImpl, 100L, resultDTO);

    assertFalse(resultDTO.isSuccess());
    assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, resultDTO.getError());
}
@Test
void testGetBlockDayCount() throws Exception {
    InstitutionChannelPymMethodDTO methodDTO = new InstitutionChannelPymMethodDTO();
    InstitutionChnnlPymMthdPscDTO pscDTO = new InstitutionChnnlPymMthdPscDTO();
    methodDTO.setBlockDayStrategyCode(EnumBlockDayStrategyCode.DAILY);

    Method method = CardProvisionServiceImpl.class.getDeclaredMethod("getBlockDayCount", InstitutionChannelPymMethodDTO.class, InstitutionChnnlPymMthdPscDTO.class);
    method.setAccessible(true);
    int blockDayCount = (int) method.invoke(cardProvisionServiceImpl, methodDTO, pscDTO);

    assertEquals(3, blockDayCount);
}
