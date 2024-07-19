 @Test
    public void testGetAvailDate_BusinessDay() {
        int blockDayCount = 5;
        Calendar nowCalendar = Calendar.getInstance();
        nowCalendar.set(Calendar.HOUR_OF_DAY, 0);
        nowCalendar.set(Calendar.MINUTE, 0);
        nowCalendar.set(Calendar.SECOND, 0);
        Date nowDate = nowCalendar.getTime();

        // Define the next business date in LocalDate
        LocalDate nextBusinessDate = LocalDate.now().plusDays(7);
        Date nextBusinessDateAsDate = DateUtils.convertLocalDateToDate(nextBusinessDate);

        // Mock the service method to return a non-null Date
        when(accountingDataLookupService.getNextBusinessDate(nowDate, blockDayCount))
                .thenReturn(nextBusinessDateAsDate);

        // Call the method under test
        LocalDate actualDate = accountingUtil.getAvailDate(EnumBlockDayType.CALENDAR_DAY, blockDayCount);

        // Additional debug information
        System.out.println("Mocked nextBusinessDateAsDate: " + nextBusinessDateAsDate);
        System.out.println("Returned date from getAvailDate: " + DateUtils.convertDateTOLocalDate(nowDate));

        // Verify the result
        assertEquals(nextBusinessDate, actualDate);
    }

org.opentest4j.AssertionFailedError: 
Expected :2024-07-26
Actual   :2024-07-24
