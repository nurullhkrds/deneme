import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.function.Executable;

import java.time.LocalDate;
import java.time.LocalDateTime;
import java.time.format.DateTimeParseException;
import java.util.Date;

import static org.junit.jupiter.api.Assertions.*;

public class DateUtilsTest {

    @Test
    void whenInstantiatingDateUtils_thenThrowException() {
        Executable executable = DateUtils::new;
        IllegalStateException exception = assertThrows(IllegalStateException.class, executable);
        assertEquals("Utility class", exception.getMessage());
    }

    @Test
    void whenFormattingLocalDateTime_thenReturnFormattedString() {
        LocalDateTime dateTime = LocalDateTime.of(2023, 7, 20, 15, 30, 45);
        String pattern = DateUtils.DATE_FORMAT_TOKEN_WITH_TIME_ZONE;
        String formatted = DateUtils.formatLocalDateTime(dateTime, pattern);
        assertEquals("20230720153045", formatted);
    }

    @Test
    void whenParsingValidLocalDateTime_thenReturnLocalDateTime() {
        String dateTimeString = "20230720153045";
        String pattern = DateUtils.DATE_FORMAT_TOKEN_WITH_TIME_ZONE;
        LocalDateTime parsedDateTime = DateUtils.parseLocalDateTime(dateTimeString, pattern);
        assertEquals(LocalDateTime.of(2023, 7, 20, 15, 30, 45), parsedDateTime);
    }

    @Test
    void whenFormattingLocalDate_thenReturnFormattedString() {
        LocalDate date = LocalDate.of(2023, 7, 20);
        String pattern = DateUtils.DATE_FORMAT_DD_MM_YYYY_WITH_SLASH;
        String formatted = DateUtils.formatLocalDate(date, pattern);
        assertEquals("20/07/2023", formatted);
    }

    @Test
    void whenParsingValidLocalDate_thenReturnLocalDate() {
        String dateString = "20/07/2023";
        String pattern = DateUtils.DATE_FORMAT_DD_MM_YYYY_WITH_SLASH;
        LocalDate parsedDate = DateUtils.parseLocalDate(dateString, pattern);
        assertEquals(LocalDate.of(2023, 7, 20), parsedDate);
    }

    @Test
    void whenConvertingLocalDateToDate_thenReturnDate() {
        LocalDate localDate = LocalDate.of(2023, 7, 20);
        Date date = DateUtils.convertLocalDateToDate(localDate);
        assertEquals(Date.from(localDate.atStartOfDay(ZoneId.systemDefault()).toInstant()), date);
    }

    @Test
    void whenConvertingDateToLocalDate_thenReturnLocalDate() {
        Date date = Date.from(LocalDate.of(2023, 7, 20).atStartOfDay(ZoneId.systemDefault()).toInstant());
        LocalDate localDate = DateUtils.convertDateTOLocalDate(date);
        assertEquals(LocalDate.of(2023, 7, 20), localDate);
    }

    @Test
    void whenGettingCurrentDate_thenReturnNonNullDate() {
        Date currentDate = DateUtils.getCurrentDate();
        assertNotNull(currentDate);
    }

    @Test
    void whenFindingRelDayDiffForLocalDate_thenReturnCorrectDays() {
        LocalDate date = LocalDate.of(2023, 7, 20);
        long daysDiff = DateUtils.findReldayDiff(date);
        LocalDate referenceDate = LocalDate.of(1957, 1, 1);
        assertEquals(referenceDate.until(date, ChronoUnit.DAYS), daysDiff);
    }

    @Test
    void whenFindingRelDayDiffForLocalDateTime_thenReturnCorrectDays() {
        LocalDateTime dateTime = LocalDateTime.of(2023, 7, 20, 15, 30, 45);
        long daysDiff = DateUtils.findRelDayDiff(dateTime);
        LocalDate referenceDate = LocalDate.of(1957, 1, 1);
        assertEquals(referenceDate.until(dateTime.toLocalDate(), ChronoUnit.DAYS), daysDiff);
    }
}
