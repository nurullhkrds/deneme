Map<String, Object> dataPack = new HashMap<>();
    dataPack.put(ProcessDataPackKey.CUSTOMER_NO.getKey(), 123L);
    dataPack.put(ProcessDataPackKey.IDENTITY_NO.getKey(), 456L);
    dataPack.put(ProcessDataPackKey.TAX_ID.getKey(), "12345");
    dataPack.put(ProcessDataPackKey.SUBSCRIBER_NO.getKey(), "subscriber123");
    dataPack.put(ProcessDataPackKey.SUBSCRIBER_NO_PART_LIST.getKey(), new ArrayList<SubscriberNoPartRequestDTO>());
