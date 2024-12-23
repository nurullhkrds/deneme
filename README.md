public List<LogRecordDTO> getFilteredLogRecords(String institutionCode, String productCode, String returnMapCode, Date startDate, Date endDate) {
    // Tarih formatını 'YYYY-MM-DD' olarak ayarla
    SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd");
    String formattedStartDate = dateFormat.format(startDate);
    String formattedEndDate = dateFormat.format(endDate);

    // Dönüştürülmüş tarihleri repository metoduna gönder
    List<Object[]> rows = repository.findLogsByCriteriaNative(institutionCode, productCode, returnMapCode, formattedStartDate, formattedEndDate);

    // Object[] verilerini DTO'ya dönüştür
    return rows.stream()
            .map(mapper::mapToLogRecordDTO)
            .toList();
}
