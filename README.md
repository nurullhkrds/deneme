@Component
@RequiredArgsConstructor
public class AccountingUtil {

    private final AccountingDataLookupService accountingDataLookupService;

    public LocalDate getAvailDate(EnumBlockDayType enumBlockDayType, Integer blockDayCount){
        Calendar nowCalendar = Calendar.getInstance();
        nowCalendar.set(Calendar.HOUR, 0);
        nowCalendar.set(Calendar.MINUTE, 0);
        nowCalendar.set(Calendar.SECOND, 0);
        nowCalendar.set(Calendar.HOUR_OF_DAY, 0);
        if(enumBlockDayType.equals(EnumBlockDayType.CALENDAR_DAY)){
            nowCalendar.add(Calendar.DAY_OF_YEAR,blockDayCount);
        }else{
            nowCalendar.setTime(accountingDataLookupService.getNextBusinessDate(nowCalendar.getTime(),blockDayCount));
        }

    return DateUtils.convertDateTOLocalDate(nowCalendar.getTime());
    }

    public static String formatCurrency(String toFormat) {
        NumberFormat formatter = NumberFormat.getInstance(Locale.GERMAN);
        formatter.setMinimumFractionDigits(2);
        formatter.setMaximumFractionDigits(2);
        BigDecimal bdToFormat = new BigDecimal(toFormat);
        return formatter.format(bdToFormat);
    }
}
