private LocalDate convertToLocalDate(Calendar calendar) {
    if (calendar == null) {
        return null;
    }
    return calendar.toInstant().atZone(ZoneId.systemDefault()).toLocalDate();
}
