LocalDate date = LocalDate.of(2024, 10, 26); // Tarihi LocalDate olarak oluştur
ZoneId zone = ZoneId.of("Europe/Istanbul"); // Belirli bir ZoneId

// LocalDate'i belirtilen zaman dilimiyle birleştirip LocalDateTime olarak başlat
LocalDateTime transactionDate = date.atStartOfDay(zone).toLocalDateTime();
