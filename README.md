@Test
public void testBeforeExecuteProcess_InstitutionChannelProcessNotActive() {
    // Testin başında mock nesnenin `null` olmadığını kontrol edelim.
    when(process.institutionChannelProcess.getIsActive()).thenReturn(false);
    assertNotNull(process.institutionChannelProcess, "institutionChannelProcess should not be null");

    BillException exception = assertThrows(BillException.class, () -> {
        process.beforeExecuteProcess();
    });

    assertEquals(EnumBillResult.INSTITUTION_PROCESS_NOT_ACTIVE.getCode(), exception.getErrorCode());
}
