@Test
void testBeforeExecuteProcess_Success() throws BillException {
    // Gerçek zamanla örtüşmesi için çalışma saatlerini ayarlayın
    when(process.processChannel.getWorkingStartTime()).thenReturn(LocalTime.of(0, 0));  // 00:00'da başlıyor
    when(process.processChannel.getWorkingFinishTime()).thenReturn(LocalTime.of(23, 59)); // 23:59'da bitiyor

    // Her şeyin doğru ayarlandığı durumda başarıyla tamamlanmalı
    process.beforeExecuteProcess();
}
