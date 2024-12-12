  private boolean isDueDateValid(XMLGregorianCalendar dueDate) {
        if (dueDate == null) {
            return false;
        }

        long todayTime = System.currentTimeMillis();

        long dueDateTime = dueDate.toGregorianCalendar().getTimeInMillis();

        return dueDateTime >= todayTime;
    }
