@Test
void testBeforeExecuteProcess_Success() throws BillException {
    // processChannel'ın çalışma saatlerini test sırasında görmezden gelmesini sağlıyoruz.
    when(process.processChannel.getWorkingStartTime()).thenReturn(LocalTime.MIN);  // 00:00
    when(process.processChannel.getWorkingFinishTime()).thenReturn(LocalTime.MAX); // 23:59:59.999

    // Her şeyin doğru ayarlandığı durumda başarıyla tamamlanmalı
    process.beforeExecuteProcess();
}
