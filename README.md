  public List<LogRecordDTO> getFilteredLogRecords(String institutionCode, String productCode, String returnMapCode, LocalDate startDate, LocalDate endDate) {
        // Native query'den gelen Object[] listesini al
        List<Object[]> rows = repository.findLogsByCriteriaNative(institutionCode, productCode, returnMapCode, startDate, endDate);

        // Object[] listesini LogRecordDTO'ya map et
        return rows.stream()
                .map(billLogMapper::mapToLogRecordDTO)
                .toList();
    }
