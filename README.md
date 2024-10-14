  @Test
    public void testGetTruncateDate() {
        // Get the truncated date from the method
        LocalDate actualDate = DateUtils.getTruncateDate();
        
        // Get the current date but without time information
        Calendar cal = Calendar.getInstance();
        cal.set(Calendar.HOUR_OF_DAY, 0);
        cal.set(Calendar.MINUTE, 0);
        cal.set(Calendar.SECOND, 0);
        cal.set(Calendar.MILLISECOND, 0);
        LocalDate expectedDate = LocalDate.ofInstant(cal.toInstant(), ZoneId.systemDefault());

        // Assert that the returned date matches the expected date
        assertThat(actualDate).isEqualTo(expectedDate);
    }
