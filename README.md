@Test
public void testAfterExecuteProcess_ErrorAndRaiseException() {
    // Gerekli nesnelerin atandığından emin olun
    process.executionOutput = mock(ProcessExecutionOutput.class); // Ensure this is not null
    process.logDTO = mock(ProcessLogDTO.class); // Ensure logDTO is not null

    // Simüle edilen bir hata durumu ayarla
    process.error = EnumBillResult.SOME_ERROR; // Hata durumunu simüle etmek için yanlış sonuç kullan
    process.shouldRaiseExceptionOnABillError = true;

    // Beklenen exception'un fırlatıldığını doğrula
    assertThrows(BillException.class, () -> {
        process.afterExecuteProcess();
    });
}
