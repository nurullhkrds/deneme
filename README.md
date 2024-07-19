@Test
public void testGetAvailDate_CalendarDay() {
    int blockDayCount = 5;
    Calendar nowCalendar = Calendar.getInstance();
    nowCalendar.set(Calendar.HOUR_OF_DAY, 0);
    nowCalendar.set(Calendar.MINUTE, 0);
    nowCalendar.set(Calendar.SECOND, 0);
    Date nowDate = nowCalendar.getTime();

    // Define the expected date 5 days from now
    LocalDate expectedDate = LocalDate.now().plusDays(blockDayCount);

    // Call the method under test
    LocalDate actualDate = accountingUtil.getAvailDate(EnumBlockDayType.CALENDAR_DAY, blockDayCount);

    // Additional debug information
    System.out.println("Expected Date: " + expectedDate);
    System.out.println("Actual Date: " + actualDate);

    // Verify the result
    assertEquals(expectedDate, actualDate);
}
