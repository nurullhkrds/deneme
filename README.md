// Gelen int tipindeki veriler
int day = 12;   // Örnek gün
int month = 10; // Örnek ay
int year = 2024; // Örnek yıl

// LocalDate oluşturma
LocalDate localDate = LocalDate.of(year, month, day);

// LocalDate'i ZonedDateTime'a çevirme
ZonedDateTime zonedDateTime = localDate.atStartOfDay(ZoneId.systemDefault());

// ZonedDateTime'i GregorianCalendar'a çevirme
GregorianCalendar gregorianCalendar = GregorianCalendar.from(zonedDateTime);

// Sonuç olarak LocalDate formatına döndürme
LocalDate finalDate = gregorianCalendar.toZonedDateTime().toLocalDate();

// Bu tarihi DTO'ya set etme
billDTO.setBillDueDate(finalDate);
