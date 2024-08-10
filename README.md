    @Test
    public void testBeforeExecuteProcess_InstitutionChannelNotWorkingHours() {
        when(process.institutionChannel.getWorkingStartTime()).thenReturn(LocalTime.of(8, 0));
        when(process.institutionChannel.getWorkingFinishTime()).thenReturn(LocalTime.of(16, 0));
        when(LocalTime.now()).thenReturn(LocalTime.of(17, 30)); // Çalışma saatleri dışında bir zaman

        BillException exception = assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });
        assertEquals("1501",exception.getErrorCode().toString());
    }
