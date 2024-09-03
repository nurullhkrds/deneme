import org.springframework.scheduling.annotation.Scheduled;
import org.springframework.stereotype.Component;

@Component
public class ScheduledTasks {

    // Her gün saat 12:00'de çalışır
    @Scheduled(cron = "0 0 12 * * ?")
    public void executeDailyTask() {
        System.out.println("Bu görev her gün saat 12:00'de çalışır.");
        // Buraya çalıştırmak istediğiniz işlevselliği ekleyin
    }
}
