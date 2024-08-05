   @BeforeEach
    void setUp() {

        when(dataPack.getDataPack((Long)ProcessDataPackKey.CUSTOMER_NO.getKey()).thenReturn(123L);
        when(dataPack.get(ProcessDataPackKey.IDENTITY_NO.getKey())).thenReturn(456L);
        when(dataPack.get(ProcessDataPackKey.TAX_ID.getKey())).thenReturn("12345");
        when(dataPack.get(ProcessDataPackKey.SUBSCRIBER_NO.getKey())).thenReturn("67890");
        when(dataPack.get(ProcessDataPackKey.SUBSCRIBER_NO_PART_LIST.getKey())).thenReturn(Collections.emptyList());
        when(dataPack.get(ProcessDataPackKey.CURRENCY.getKey())).thenReturn("USD");

        when(paymentUtilImpl.isFomOperationEnabled(any())).thenReturn(true);
    }
