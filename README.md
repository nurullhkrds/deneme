@Test
void testBeforeExecuteProcess_Success() throws BillException {
    // Şu anki zamana uygun bir çalışma saatleri aralığı belirleyin
    when(process.processChannel.getWorkingStartTime()).thenReturn(LocalTime.of(0, 0));  // 00:00'da başlasın
    when(process.processChannel.getWorkingFinishTime()).thenReturn(LocalTime.of(23, 59)); // 23:59'da bitsin

    // Her şeyin doğru ayarlandığı durumda başarıyla tamamlanmalı
    process.beforeExecuteProcess();
}
