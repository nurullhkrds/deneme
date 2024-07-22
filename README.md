ublic class DateUtils {
    //TODO common a tasinacak
    private DateUtils() {
        throw new IllegalStateException("Utility class");
    }

    public static final String DATE_FORMAT_TOKEN_WITH_TIME_ZONE = "yyyyMMddHHmmss";

    public static final String DATE_FORMAT_YYYY_MM_DD_HH_MM_SS_SSS = "yyyyMMddHHmmssSSS";

    public static final String DATE_FORMAT_DD_MM_YYYY_WITH_SLASH = "dd/MM/yyyy";

    public static final String DATE_FORMAT_YYYY_MM_DD_WITH_HYPHEN = "yyyy-MM-dd";

    public static String formatLocalDateTime(LocalDateTime ldt, String pattern) {
        DateTimeFormatter customFormatter = DateTimeFormatter.ofPattern(pattern);
        return ldt.format(customFormatter);
    }

    public static LocalDateTime parseLocalDateTime(String ldtStr, String pattern) {
        DateTimeFormatter customFormatter = DateTimeFormatter.ofPattern(pattern);
        return LocalDateTime.parse(ldtStr, customFormatter);
    }

    public static String formatLocalDate(LocalDate ld, String pattern) {
        DateTimeFormatter customFormatter = DateTimeFormatter.ofPattern(pattern);
        return ld.format(customFormatter);
    }

    public static LocalDate parseLocalDate(String ldStr, String pattern) {
        DateTimeFormatter customFormatter = DateTimeFormatter.ofPattern(pattern);
        return LocalDate.parse(ldStr, customFormatter);
    }

    public static Date convertLocalDateToDate(LocalDate localDate) {
        return Date.from(localDate.atStartOfDay(ZoneId.systemDefault()).toInstant());
    }

    public static LocalDate convertDateTOLocalDate(Date date) {
        return date.toInstant()
                .atZone(ZoneId.systemDefault())
                .toLocalDate();
    }

    public static Date getCurrentDate() {
        return Calendar.getInstance().getTime();
    }

    public static long findReldayDiff(LocalDate ld) {
        LocalDate firstDate = LocalDate.of(1957, 01, 01);
        return firstDate.until(ld, ChronoUnit.DAYS);
    }

    public static long findRelDayDiff(LocalDateTime ld) {
        return findReldayDiff(ld.toLocalDate());
    }
}
