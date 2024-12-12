private boolean isDueDateValid(XMLGregorianCalendar dueDate) {
    if (dueDate == null) {
        return false;
    }

    // Bugünün tarihi, sadece gün, ay ve yıl olarak alınır.
    LocalDate todayDate = LocalDate.now();

    // DueDate'i LocalDate'e dönüştür.
    LocalDate dueLocalDate = dueDate.toGregorianCalendar().toZonedDateTime().toLocalDate();

    // Tarih karşılaştırmasını gün, ay ve yıl bazında yapar.
    return !dueLocalDate.isBefore(todayDate);
}
