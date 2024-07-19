import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.BeforeEach;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.mockito.stubbing.Answer;
import java.time.LocalDate;
import java.util.Calendar;
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.Mockito.when;

public class AccountingUtilTest {

    @Mock
    private AccountingDataLookupService accountingDataLookupService;

    @InjectMocks
    private AccountingUtil accountingUtil;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    public void testGetAvailDate_CalendarDay() {
        int blockDayCount = 5;
        Calendar nowCalendar = Calendar.getInstance();
        nowCalendar.set(Calendar.HOUR, 0);
        nowCalendar.set(Calendar.MINUTE, 0);
        nowCalendar.set(Calendar.SECOND, 0);
        nowCalendar.set(Calendar.HOUR_OF_DAY, 0);
        LocalDate expectedDate = DateUtils.convertDateTOLocalDate(nowCalendar.getTime());
        
        // Expected date should be 5 days from now
        LocalDate actualDate = accountingUtil.getAvailDate(EnumBlockDayType.CALENDAR_DAY, blockDayCount);
        assertEquals(expectedDate.plusDays(blockDayCount), actualDate);
    }

    @Test
    public void testGetAvailDate_BusinessDay() {
        int blockDayCount = 5;
        Calendar nowCalendar = Calendar.getInstance();
        nowCalendar.set(Calendar.HOUR, 0);
        nowCalendar.set(Calendar.MINUTE, 0);
        nowCalendar.set(Calendar.SECOND, 0);
        nowCalendar.set(Calendar.HOUR_OF_DAY, 0);

        LocalDate nextBusinessDate = LocalDate.now().plusDays(7); // Assume business day calculation adds 7 days
        when(accountingDataLookupService.getNextBusinessDate(nowCalendar.getTime(), blockDayCount))
                .thenAnswer((Answer<Date>) invocation -> DateUtils.convertLocalDateToDate(nextBusinessDate));
        
        LocalDate actualDate = accountingUtil.getAvailDate(EnumBlockDayType.BUSINESS_DAY, blockDayCount);
        assertEquals(nextBusinessDate, actualDate);
    }

 @Test
    public void testFormatCurrency() {
        String toFormat = "123456.789";
        String formatted = AccountingUtil.formatCurrency(toFormat);
        assertEquals("123.456,79", formatted); // Almanca formatına göre
    }

    @Test
    public void testFormatCurrency_WithTrailingZeros() {
        String toFormat = "123.4";
        String formatted = AccountingUtil.formatCurrency(toFormat);
        assertEquals("123,40", formatted); // Almanca formatına göre
    }

    @Test
    public void testFormatCurrency_WithNegativeValue() {
        String toFormat = "-123456.789";
        String formatted = AccountingUtil.formatCurrency(toFormat);
        assertEquals("-123.456,79", formatted); // Almanca formatına göre
    }
}
