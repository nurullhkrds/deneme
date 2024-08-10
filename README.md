@Test
public void testBeforeExecuteProcess_InstitutionNotActive() {
    // lenient() kullanarak gereksiz stub uyarısını bastırın
    lenient().when(institution.getIsActive()).thenReturn(false);
    
    assertThrows(BillException.class, () -> {
        process.beforeExecuteProcess();
    });
}
