import org.springframework.stereotype.Component;
import java.time.LocalDate;
import java.sql.Timestamp;

@Component
public class LogRecordMapper {

    public LogRecordDTO mapToLogRecordDTO(Object[] row) {
        LogRecordDTO dto = new LogRecordDTO();

        // Sorgudan gelen sütunları sırasıyla set et
        dto.setSubscriberNo((String) row[0]);
        dto.setLogDate(convertToLocalDate(row[1]));
        dto.setReceivedData((String) row[2]);
        dto.setSendData((String) row[3]);
        dto.setInstitutionReturnCode((String) row[4]);
        dto.setReturnMapCode((String) row[5]);
        dto.setInstitutionReturnText((String) row[6]);
        dto.setBankReturnCode((String) row[7]);
        dto.setInstitutionCode((String) row[8]);
        dto.setProductCode((String) row[9]);

        return dto;
    }

    // Yardımcı metod: Object'i LocalDate'e dönüştür
    private LocalDate convertToLocalDate(Object date) {
        if (date instanceof Timestamp) {
            return ((Timestamp) date).toLocalDateTime().toLocalDate();
        }
        return null;
    }
}
@Service
@RequiredArgsConstructor
public class PaidBillLogService implements IPaidBillLogService {

    private final BillPaymentRestFacade facade;
    private final BillLogMapper billLogMapper;
    private final RemoteServiceLogRepository repository;
    private final LogRecordMapper logRecordMapper;

    @Override
    public List<LogRecordDTO> getFilteredLogRecords(String institutionCode, String productCode, String returnMapCode, LocalDate startDate, LocalDate endDate) {
        // Native query'den gelen Object[] listesini al
        List<Object[]> rows = repository.findLogsByCriteriaNative(institutionCode, productCode, returnMapCode, startDate, endDate);

        // Object[] listesini LogRecordDTO'ya map et
        List<LogRecordDTO> logRecords = rows.stream()
                .map(logRecordMapper::mapToLogRecordDTO)
                .toList();

        return logRecords;
    }
}
