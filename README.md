@Test
void testBeforeExecuteProcess_Success() throws BillException {
    // Çalışma saatlerini belirli sabit zamanlara ayarlayın
    when(process.processChannel.getWorkingStartTime()).thenReturn(LocalTime.of(8, 0));  // 08:00'da başlıyor
    when(process.processChannel.getWorkingFinishTime()).thenReturn(LocalTime.of(17, 0)); // 17:00'da bitiyor

    // Her şeyin doğru ayarlandığı durumda başarıyla tamamlanmalı
    process.beforeExecuteProcess();
}
