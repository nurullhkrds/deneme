@Override
public CreateReverseAccountingResultDTO doReverseAccounting(CreateReverseAccountingDTO createReverseAccountingDTO) {
    CreateReverseAccountingResultDTO createReverseAccountingResultDTO = new CreateReverseAccountingResultDTO();
    MakeReverseProvisionRequest makeReverseProvisionRequest = prepareReverseProvisionRequest(createReverseAccountingDTO);
    try {
        MakeReverseProvisionResponse makeReverseProvision = provisionNextService.makeReverseProvision(makeReverseProvisionRequest);

        if (!makeReverseProvision.isSuccess()) {
            handleException(makeReverseProvision.getErrorCode(), createReverseAccountingResultDTO);
            createReverseAccountingResultDTO.setSuccess(false);
            return createReverseAccountingResultDTO;
        }
        createReverseAccountingResultDTO.setSuccess(true);
    } catch (Exception e) {
        if (e.getCause() != null && e.getCause().getClass().equals(ServiceCallException.class)) {
            Long errorCode = ((ServiceCallException) e.getCause()).getErrorCode();
            handleException(errorCode, createReverseAccountingResultDTO);
            return createReverseAccountingResultDTO;
        }
        createReverseAccountingResultDTO.setError(EnumBillResult.GENERIC_UNKNOWN_ERROR);
        createReverseAccountingResultDTO.setSuccess(false);
    }

    return createReverseAccountingResultDTO;
}
@Test
public void testDoReverseAccounting_GeneralException() throws ServiceCallException {
    CreateReverseAccountingDTO inputDTO = new CreateReverseAccountingDTO();
    inputDTO.setChannelTransactionId("12345");
    inputDTO.setContractNo(987654L);

    MakeReverseProvisionRequest mockRequest = new MakeReverseProvisionRequest();
    mockRequest.setTransactionId(inputDTO.getChannelTransactionId());
    mockRequest.setContractNo(inputDTO.getContractNo());
    mockRequest.setReverseDescriptionAppendix("İPTAL");

    when(provisionNextService.makeReverseProvision(any())).thenThrow(new RuntimeException("Test exception"));

    CreateReverseAccountingResultDTO resultDTO = accountReverseProvisionService.doReverseAccounting(inputDTO);

    verify(provisionNextService, times(1)).makeReverseProvision(any());
    assertFalse(resultDTO.isSuccess());
    assertEquals(EnumBillResult.GENERIC_UNKNOWN_ERROR, resultDTO.getError());
}
