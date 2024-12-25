public interface RemoteServiceLogRepository extends JpaRepository<RemoteServiceLog, Long> {
    List<RemoteServiceLog> findAllByInstitutionIdAndServiceTypeAndLogDateBetween(
        Long institutionId,
        String serviceType,
        LocalDate startDate,
        LocalDate endDate
    );
}
