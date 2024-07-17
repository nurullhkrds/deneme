@Test
public void testDoReverseAccounting_ServiceCallException() throws ServiceCallException {
    CreateReverseAccountingDTO inputDTO = new CreateReverseAccountingDTO();
    inputDTO.setChannelTransactionId("12345");
    inputDTO.setContractNo(987654L);

    MakeReverseProvisionRequest mockRequest = new MakeReverseProvisionRequest();
    mockRequest.setTransactionId(inputDTO.getChannelTransactionId());
    mockRequest.setContractNo(inputDTO.getContractNo());
    mockRequest.setReverseDescriptionAppendix("İPTAL");

    // RuntimeException içerisine ServiceCallException'ı sararak fırlatma
    when(provisionNextService.makeReverseProvision(any())).thenThrow(new RuntimeException(new ServiceCallException(new ExceptionData())));

    CreateReverseAccountingResultDTO resultDTO = accountReverseProvisionService.doReverseAccounting(inputDTO);

    verify(provisionNextService, times(1)).makeReverseProvision(any());
    assertFalse(resultDTO.isSuccess());
    assertNotNull(resultDTO.getError());
    assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, resultDTO.getError());
}
