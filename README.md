  @Override
    public List<LogRecordDTO> getFilteredLogRecords(String institutionCode, String productCode, String returnMapCode, Date startDate, Date endDate) {
        List<Object[]> rows = repository.findLogsByCriteriaNative(institutionCode, productCode, returnMapCode, startDate, endDate);

        return rows.stream()
                .map(mapper::mapToLogRecordDTO)
                .toList();
    }
