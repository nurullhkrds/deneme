   when(dataPack.get((Long) ProcessDataPackKey.CUSTOMER_NO.getKey())).thenReturn(123L);
        when(dataPack.get((Long) ProcessDataPackKey.IDENTITY_NO.getKey())).thenReturn(456L);
        when(dataPack.get((String) ProcessDataPackKey.TAX_ID.getKey())).thenReturn("12345");
        when(dataPack.get((String) ProcessDataPackKey.SUBSCRIBER_NO.getKey())).thenReturn("67890");
        when(dataPack.get((List<SubscriberNoPartRequestDTO>) ProcessDataPackKey.SUBSCRIBER_NO_PART_LIST.getKey())).thenReturn(Collections.emptyList());
        when(dataPack.get((String) ProcessDataPackKey.CURRENCY.getKey())).thenReturn("USD");

        when(paymentUtilImpl.isFomOperationEnabled(any())).thenReturn(true);
