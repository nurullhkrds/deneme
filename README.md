import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import java.util.List;

public interface RemoteServiceLogRepository extends JpaRepository<RemoteServiceLog, Long> {
    @Query(value = "SELECT * FROM RemoteServiceLog r WHERE r.institutionId = :institutionId AND r.serviceType = :serviceType AND r.logDate BETWEEN to_date(:startDate, 'DD.MM.YYYY') AND to_date(:endDate, 'DD.MM.YYYY')", nativeQuery = true)
    List<RemoteServiceLog> findByInstitutionIdAndServiceTypeAndLogDateBetween(Long institutionId, String serviceType, String startDate, String endDate);
}


@Service
public class RemoteServiceLogService {

    private final RemoteServiceLogRepository repository;

    public RemoteServiceLogService(RemoteServiceLogRepository repository) {
        this.repository = repository;
    }

    public List<RemoteServiceLog> getLogs(Long institutionId, String serviceType, String startDateStr, String endDateStr) {
        DateTimeFormatter inputFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd");
        DateTimeFormatter outputFormatter = DateTimeFormatter.ofPattern("dd.MM.yyyy");
        
        LocalDate startDate = LocalDate.parse(startDateStr, inputFormatter);
        LocalDate endDate = LocalDate.parse(endDateStr, inputFormatter);
        
        String formattedStartDate = startDate.format(outputFormatter);
        String formattedEndDate = endDate.format(outputFormatter);
        
        return repository.findByInstitutionIdAndServiceTypeAndLogDateBetween(institutionId, serviceType, formattedStartDate, formattedEndDate);
    }
}
