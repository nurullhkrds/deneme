@Test
void testExecuteProcess_Debug() throws BillException {
    // Mockları ayarlayın
    when(paymentUtilImpl.isFomOperationEnabled(any(InstitutionDTO.class))).thenReturn(true);

    QueriedBillDTO billDTO = new QueriedBillDTO();
    billDTO.setBillNo("123");
    billDTO.setBillAmount(BigDecimal.valueOf(100));
    List<QueriedBillDTO> billList = new ArrayList<>();
    billList.add(billDTO);

    QueryBillsAdapterResponse response = new QueryBillsAdapterResponse();
    response.setInternalResultCode("00");
    response.setBills(billList);

    when(adapterService.queryBills(any(QueryBillsAdapterRequest.class), anyString(), anyString()))
            .thenReturn(response);

    when(provisionService.createProvisions(anyList())).thenReturn(new ArrayList<>());

    process.setDataPack(new HashMap<>());
    process.getDataPack().put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 123L);
    process.getDataPack().put(ProcessDataPackKey.IDENTITY_NO.getKey(), 456L);
    process.getDataPack().put(ProcessDataPackKey.SUBSCRIBER_NO_PART_LIST.getKey(), subscriberNoPartList);

    // Process'i çalıştırın ve adım adım kontrol edin
    process.executeProcess();

    // GatherData kontrolü
    assertNotNull(process.getInstitution());
    assertNotNull(process.getInstitutionProcess());

    // QueriedBillDTOList'in dolu olup olmadığını kontrol edin
    assertNotNull(process.getQueriedBillDTOList());
    assertFalse(process.getQueriedBillDTOList().isEmpty(), "QueriedBillDTOList should not be empty");

    // CreateProvisions adımının tetiklenip tetiklenmediğini kontrol edin
    verify(provisionService, times(1)).createProvisions(anyList());

    // Son olarak error'ın null olduğunu kontrol edin
    assertNull(process.getExecutionOutput().getResult(), "Error should be null");
}
