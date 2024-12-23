import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;
import java.util.List;

public interface RemoteServiceLogRepository extends JpaRepository<RemoteServiceLog, Long> {
    @Query(value = "SELECT new LogRecordDTO(rsl.subscriberNo, rsl.createDate, rsl.receivedData, rsl.sendData, rsl.institutionReturnCode, rm.returnMapCode, rm.institutionReturnText, rm.bankReturnCode, inst.institutionCode, inst.productCode) " +
                   "FROM RemoteServiceLog rsl " +
                   "JOIN ReturnMap rm ON rsl.institutionReturnCode = rm.institutionReturnCode AND rsl.bankReturnCode = rm.bankReturnCode " +
                   "JOIN Institution inst ON rsl.institutionId = inst.id " +
                   "WHERE rsl.serviceType = 'NOTIFY_PAYMENT' " +
                   "AND inst.institutionCode = :institutionCode " +
                   "AND inst.productCode = :productCode " +
                   "AND rm.returnMapCode = :returnMapCode " +
                   "AND rsl.logDate BETWEEN :startDate AND :endDate", nativeQuery = false)
    List<LogRecordDTO> findLogsByCriteria(@Param("institutionCode") String institutionCode,
                                          @Param("productCode") String productCode,
                                          @Param("returnMapCode") String returnMapCode,
                                          @Param("startDate") LocalDate startDate,
                                          @Param("endDate") LocalDate endDate);
}
import org.springframework.stereotype.Service;
import java.time.LocalDate;
import java.util.List;

@Service
public class LogRecordService {
    private final RemoteServiceLogRepository remoteServiceLogRepository;

    public LogRecordService(RemoteServiceLogRepository remoteServiceLogRepository) {
        this.remoteServiceLogRepository = remoteServiceLogRepository;
    }

    public List<LogRecordDTO> getFilteredLogRecords(String institutionCode, String productCode, String returnMapCode, LocalDate startDate, LocalDate endDate) {
        return remoteServiceLogRepository.findLogsByCriteria(institutionCode, productCode, returnMapCode, startDate, endDate);
    }
}

private String subscriberNo;
    private LocalDate logDate;
    private String receivedData;
    private String sendData;
    private String institutionReturnCode;
    private String returnMapCode;
    private String institutionReturnText;
    private String bankReturnCode;
    private String institutionCode;
    private String productCode;
