  @Test
    public void testGetAvailDate_BusinessDay() {
        int blockDayCount = 5;
        Calendar nowCalendar = Calendar.getInstance();
        nowCalendar.set(Calendar.HOUR, 0);
        nowCalendar.set(Calendar.MINUTE, 0);
        nowCalendar.set(Calendar.SECOND, 0);
        nowCalendar.set(Calendar.HOUR_OF_DAY, 0);
        Date nowDate = nowCalendar.getTime();

        // Define the next business date in LocalDate
        LocalDate nextBusinessDate = LocalDate.now().plusDays(7);
        Date nextBusinessDateAsDate = DateUtils.convertLocalDateToDate(nextBusinessDate);

        // Mock the service method
        when(accountingDataLookupService.getNextBusinessDate(nowDate, blockDayCount))
                .thenReturn(nextBusinessDateAsDate);

        // Call the method under test
        LocalDate actualDate = accountingUtil.getAvailDate(EnumBlockDayType.BUSINESS_DAY, blockDayCount);

        // Verify the result
        assertEquals(nextBusinessDate, actualDate);
    }
