import org.springframework.data.jpa.repository.JpaRepository;
import java.util.List;

public interface RemoteServiceLogRepository extends JpaRepository<RemoteServiceLog, Long> {
    List<RemoteServiceLog> findAllByInstitutionIdAndServiceTypeAndLogDateBetween(
        Long institutionId,
        String serviceType,
        String startDate,  // String olarak kabul ediliyor
        String endDate
    );
}
DateTimeFormatter inputFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd");
        DateTimeFormatter outputFormatter = DateTimeFormatter.ofPattern("dd.MM.yyyy");
        
        LocalDate startDate = LocalDate.parse(startDateStr, inputFormatter);
        LocalDate endDate = LocalDate.parse(endDateStr, inputFormatter);
        
        String formattedStartDate = startDate.format(outputFormatter);
        String formattedEndDate = endDate.format(outputFormatter);
