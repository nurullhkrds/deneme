@Test
void testBeforeExecuteProcess_Success() throws BillException {
    // Çalışma saatlerini şu anki zaman dilimine uyacak şekilde ayarlayın
    when(process.processChannel.getWorkingStartTime()).thenReturn(LocalTime.now().minusHours(1));  // 1 saat önce başlasın
    when(process.processChannel.getWorkingFinishTime()).thenReturn(LocalTime.now().plusHours(1));  // 1 saat sonra bitsin

    // Her şeyin doğru ayarlandığı durumda başarıyla tamamlanmalı
    process.beforeExecuteProcess();
}
