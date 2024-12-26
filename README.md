    @Query(value = "SELECT rsl.subscriber_no, rsl.create_date, rsl.received_data, rsl.send_data, " +
            "rsl.institution_return_code, rm.return_map_code, rm.institution_return_text, " +
            "rm.bank_return_code, inst.institution_code, inst.product_code " +
            "FROM REMOTE_SERVICE_LOG rsl " +
            "JOIN RETURN_MAP rm ON rsl.institution_return_code = rm.institution_return_code " +
            "AND rsl.bank_return_code = rm.bank_return_code " +
            "JOIN INSTITUTION inst ON rsl.institution_id = inst.id " +
            "WHERE rsl.service_type = 'NOTIFY_PAYMENT' " +
            "AND inst.institution_code = :institutionCode " +
            "AND inst.product_code = :productCode " +
            "AND rm.return_map_code = :returnMapCode " +
            "AND rsl.log_date BETWEEN TO_DATE(:startDate, 'YYYY-MM-DD') AND TO_DATE(:endDate, 'YYYY-MM-DD')",
            nativeQuery = true)
    List<Object[]> findLogsByCriteriaNative(@Param("institutionCode") String institutionCode,
                                            @Param("productCode") String productCode,
                                            @Param("returnMapCode") String returnMapCode,
                                            @Param("startDate") String startDate,
                                            @Param("endDate") String endDate);

















[
    {
        "createDate": "2023-07-28T11:12:19",
        "createdBy": "SYSTEM",
        "updateDate": null,
        "updatedBy": null,
        "id": 200031,
        "institutionId": 50002,
        "serviceType": "NOTIFY_PAYMENT",
        "subscriberNo": null,
        "logDate": "2023-07-28",
        "duration": 906,
        "sendData": "{\"fatura_id\":\"253\",\"hizmet_id\":\"6400000001\"}",
        "receivedData": "{\"hata\":false,\"aciklama\":null,\"durum_kodu\":1000,\"data\":\"Ödeme İşlemi başarılı.\"}",
        "institutionReturnCode": "1000",
        "bankReturnCode": "00000",
        "additionalInfo": "f930e763-28f8-4091-9b4a-36397b97afd8",
        "dataPowerTransactionId": null,
        "channelCode": "602",
        "branchCode": "326",
        "channelTransactionId": null,
        "channelSessionId": null
    },
    {
        "createDate": "2023-08-01T10:05:56",
        "createdBy": "SYSTEM",
        "updateDate": null,
        "updatedBy": null,
        "id": 250042,
        "institutionId": 50002,
        "serviceType": "NOTIFY_PAYMENT",
        "subscriberNo": null,
        "logDate": "2023-08-01",
        "duration": 708,
        "sendData": "{\"fatura_id\":\"260\",\"hizmet_id\":\"6400000001\"}",
        "receivedData": "{\"hata\":false,\"aciklama\":null,\"durum_kodu\":1000,\"data\":\"Ödeme İşlemi başarılı.\"}",
        "institutionReturnCode": "1000",
        "bankReturnCode": "00000",
        "additionalInfo": "02b169bd-e782-448a-8d00-d799273aa53d",
        "dataPowerTransactionId": null,
        "channelCode": "602",
        "branchCode": null,
        "channelTransactionId": null,
        "channelSessionId": null
    },
]
