@Test
public void testBeforeExecuteProcess_ProcessChannelNotWorkingHours() {
    // Burada LocalTime.now() metodu olduğu gibi kullanılıyor. 
    // Saat testin çalışma saatlerinin dışında olmalı.
    
    // Bu, `processChannel`'ın çalışma saatlerini mockluyor.
    when(process.processChannel.getWorkingStartTime()).thenReturn(LocalTime.of(8, 0));
    when(process.processChannel.getWorkingFinishTime()).thenReturn(LocalTime.of(16, 0));

    // LocalTime.now() doğal olarak çalışma saatleri dışında olmalı.
    BillException exception = assertThrows(BillException.class, () -> {
        process.beforeExecuteProcess();
    });
    
    assertEquals("1501", exception.getErrorCode().toString());
}
