LocalDateTime localDateTime = LocalDateTime.of(2024, 10, 26, 0, 0);
        ZonedDateTime zonedDateTime = localDateTime.atZone(ZoneId.of("UTC+03:00"));

        // ISO 8601 formatına dönüştürmek için:
        String formattedDateTime = zonedDateTime.format(DateTimeFormatter.ISO_OFFSET_DATE_TIME);
