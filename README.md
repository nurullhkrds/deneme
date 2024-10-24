public String generateInvoiceNumber(int systemId, int accountId, int declarationMainId, int declarationSequenceNo, int year, int installment, LocalDate dueDate) {
    DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyyMMdd");
    String dueDateStr = dueDate.format(formatter);
    return systemId + "-" + accountId + "-" + declarationMainId + "-" + declarationSequenceNo + "-" + year + "-" + installment + "-" + dueDateStr;
}
