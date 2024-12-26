 private List<LogRecordDTO> joinAndFilterLogRecords(String institutionCode, String productCode,String serviceType, String returnMapCode, Date startDate, Date endDate)
            throws MicroException {
        SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd");
        String formattedStartDate = dateFormat.format(startDate);
        String formattedEndDate = dateFormat.format(endDate);
        DateTimeFormatter outputFormatter = DateTimeFormatter.ofPattern("dd.MM.yyyy");
