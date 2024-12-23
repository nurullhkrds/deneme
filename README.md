@Repository
public interface RemoteServiceLogRepository extends JpaRepository<RemoteServiceLog, Long> {

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
                   "AND rsl.log_date BETWEEN :startDate AND :endDate", 
           nativeQuery = true)
    List<Object[]> findLogsByCriteriaNative(@Param("institutionCode") String institutionCode,
                                            @Param("productCode") String productCode,
                                            @Param("returnMapCode") String returnMapCode,
                                            @Param("startDate") LocalDate startDate,
                                            @Param("endDate") LocalDate endDate);
}
