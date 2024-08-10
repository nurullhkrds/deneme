@Test
void testInstitutionChannelProcessWorkingTimeError() {
    when(process.institutionChannelProcess.getWorkingStartTime()).thenReturn(LocalTime.of(8, 0));
    when(process.institutionChannelProcess.getWorkingFinishTime()).thenReturn(LocalTime.of(16, 0));
    // Testin çalıştığı saat 08:00 ile 16:00 arasında olmalı.

    BillException exception = assertThrows(BillException.class, () -> {
        process.beforeExecuteProcess();
    });

    assertEquals(EnumBillResult.INSTITUTION_PROCESS_CHANNEL_WORKING_TIME_ERROR.getCode(), exception.getResultCode());
}
