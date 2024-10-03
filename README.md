if (row[12] != null) {
    dto.setUpdateDate(((Date) row[12]).toInstant()
                     .atZone(ZoneId.systemDefault())
                     .toLocalDateTime());
