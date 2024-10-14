public class DateUtils {
    //TODO common a tasinacak
    private DateUtils() {
        throw new IllegalStateException("Utility class");
    }

    public static final String DATE_FORMAT_TOKEN_WITH_TIME_ZONE = "yyyyMMddHHmmss";

    public static final String DATE_FORMAT_YYYY_MM_DD_HH_MM_SS_SSS = "yyyyMMddHHmmssSSS";

    public static final String DATE_FORMAT_DD_MM_YYYY_WITH_SLASH = "dd/MM/yyyy";

    public static final String DATE_FORMAT_YYYY_MM_DD_WITH_HYPHEN = "yyyy-MM-dd";   

 public static LocalDate getTruncateDate() {
        Calendar cal = Calendar.getInstance();
        cal.set(Calendar.HOUR_OF_DAY, 0);
        cal.set(Calendar.MINUTE, 0);
        cal.set(Calendar.SECOND, 0);
        cal.set(Calendar.MILLISECOND, 0);
        return  LocalDate.ofInstant(cal.toInstant(), ZoneId.systemDefault());
    }
