
    @Query(value = "SELECT rsl.institution_id," +
            "rsl.subscriber_no, " +
            "rsl.create_date," +
            "rsl.institution_return_code," +
            "rsl.bank_return_code " +
            " rsl.received_data, rsl.send_data, " +
            "FROM REMOTE_SERVICE_LOG rsl " +
            "WHERE rsl.service_type = 'NOTIFY_PAYMENT' " +
            "AND rsl.institution_id = :institutionId " +
            "AND rsl.log_date BETWEEN TO_DATE(:startDate, 'YYYY-MM-DD') AND TO_DATE(:endDate, 'YYYY-MM-DD')",
            nativeQuery = true)
    List<Object[]> findLogsByParameters(@Param("institutionId") Long institutionId,
                                            @Param("startDate") String startDate,
                                            @Param("endDate") String endDate);


    List<RemoteServiceLog> findAllByInstitutionIdAndServiceTypeAndLogDateBetween(Long institutionId,
                                                                                             String serviceType,
                                                                                             String startDate,
                                                                                              String endDate);
