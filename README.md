  private boolean isDueDateValid(XMLGregorianCalendar dueDate) {
        if (dueDate == null) {
            return false;
        }
        LocalDate todayDate = LocalDate.now();
        LocalDate dueLocalDate = dueDate.toGregorianCalendar().toZonedDateTime().toLocalDate();
        return !dueLocalDate.isBefore(todayDate);
    }

isDueDateValid(aboneBorc.getSonOdemeTarih()))
