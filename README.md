private LocalDate convertToLocalDate(XMLGregorianCalendar xmlGregorianCalendar) {
    if (xmlGregorianCalendar == null) {
        return null;
    }
    return xmlGregorianCalendar.toGregorianCalendar().toZonedDateTime().toLocalDate();
}
