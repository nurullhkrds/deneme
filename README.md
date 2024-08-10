@Test
public void testAfterExecuteProcess_ErrorAndRaiseException() {
    // Gerekli nesnelerin atanmasını sağlayın
    process.executionOutput = mock(ProcessExecutionOutput.class); // Non-null assignment
    process.logDTO = mock(ProcessLogDTO.class); // Ensure logDTO is assigned

    // Hata durumu oluşturun
    process.error = EnumBillResult.SOME_ERROR; // Hata durumunu ayarla
    process.shouldRaiseExceptionOnABillError = true;

    // Beklenen exception'un fırlatıldığını doğrulayın
    assertThrows(BillException.class, () -> {
        process.afterExecuteProcess();
    });
}
