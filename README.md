@Test
void testBeforeExecuteProcess_Success() throws BillException {
    // LocalTime.now() şu anki zamana göre ayarlandığı için, bunu test sırasında kontrol edin
    LocalTime now = LocalTime.now();

    // `processChannel` çalışma saatlerini şu anki zaman aralığına uyacak şekilde ayarlayın
    when(process.processChannel.getWorkingStartTime()).thenReturn(now.minusHours(1)); // 1 saat önce başlasın
    when(process.processChannel.getWorkingFinishTime()).thenReturn(now.plusHours(1)); // 1 saat sonra bitsin

    // Her şeyin doğru ayarlandığı durumda başarıyla tamamlanmalı
    process.beforeExecuteProcess();
}
