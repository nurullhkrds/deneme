@Test
void testExecuteProcess_Success() throws BillException {
    // `FomOperationEnabled` durumu true döndürsün
    when(paymentUtilImpl.isFomOperationEnabled(any(InstitutionDTO.class))).thenReturn(true);

    // Mocked response ile `queriedBillDTOList` dolduruluyor
    QueriedBillDTO billDTO = new QueriedBillDTO();
    billDTO.setBillNo("123");
    billDTO.setBillAmount(BigDecimal.valueOf(100));
    List<QueriedBillDTO> billList = new ArrayList<>();
    billList.add(billDTO);

    QueryBillsAdapterResponse response = new QueryBillsAdapterResponse();
    response.setInternalResultCode("00");
    response.setBills(billList);

    // Mock `adapterService.queryBills` çalıştığında bu yanıtı döndürsün
    when(adapterService.queryBills(any(QueryBillsAdapterRequest.class), anyString(), anyString()))
            .thenReturn(response);

    // `provisionService.createProvisions` metodunu mockla
    when(provisionService.createProvisions(anyList())).thenReturn(new ArrayList<>());

    // `dataPack` ve diğer gerekli alanlar ayarlanıyor
    process.setDataPack(new HashMap<>());
    process.getDataPack().put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 123L);
    process.getDataPack().put(ProcessDataPackKey.IDENTITY_NO.getKey(), 456L);
    process.getDataPack().put(ProcessDataPackKey.SUBSCRIBER_NO_PART_LIST.getKey(), subscriberNoPartList);

    // Process çalıştırılıyor
    process.executeProcess();

    // `CreateProvisions` adımının çağrıldığını doğrula
    verify(provisionService, times(1)).createProvisions(anyList());

    // Başka gerekli doğrulamalar
    verify(paymentEventPublisher, times(1)).publishInquiryLimiationNotification(any());

    assertNull(process.getExecutionOutput());
}
