@Test
void testExecuteProcess_CreateProvisions() throws BillException {
    // Adım 1: `QueryFromService` adımının `queriedBillDTOList`'i doldurmasını sağla
    QueriedBillDTO billDTO = new QueriedBillDTO();
    billDTO.setBillNo("123");
    billDTO.setBillAmount(BigDecimal.valueOf(100));
    List<QueriedBillDTO> billList = new ArrayList<>();
    billList.add(billDTO);

    QueryBillsAdapterResponse response = new QueryBillsAdapterResponse();
    response.setInternalResultCode("00"); // Başarı kodu
    response.setBills(billList);

    when(adapterService.queryBills(any(QueryBillsAdapterRequest.class), anyString(), anyString()))
        .thenReturn(response);

    // Adım 2: Provisions işlemi için mock ayarları
    when(provisionService.createProvisions(anyList())).thenReturn(new ArrayList<>());

    // Adım 3: Gerekli `DataPack` ayarlarını yap
    process.setDataPack(new HashMap<>());
    process.getDataPack().put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 123L);
    process.getDataPack().put(ProcessDataPackKey.IDENTITY_NO.getKey(), 456L);
    process.getDataPack().put(ProcessDataPackKey.SUBSCRIBER_NO_PART_LIST.getKey(), subscriberNoPartList);

    // Adım 4: Process'i çalıştır
    process.executeProcess();

    // Adım 5: `createProvisions` metodunun çağrıldığını doğrula
    verify(provisionService, times(1)).createProvisions(anyList());
}
