@Test
public void testBeforeExecuteProcess_Success() throws BillException {
    // Eklenen loglar ve assert ifadeleri ile kontrol
    assertNotNull(process.processChannel, "processChannel is null");
    assertNotNull(process.processChannel.getIsActive(), "processChannel.getIsActive() is null");
    assertNotNull(process.processChannel.getWorkingStartTime(), "processChannel.getWorkingStartTime() is null");
    assertNotNull(process.processChannel.getWorkingFinishTime(), "processChannel.getWorkingFinishTime() is null");

    assertNotNull(process.institution, "institution is null");
    assertNotNull(process.institution.getIsActive(), "institution.getIsActive() is null");

    assertNotNull(process.institutionProcess, "institutionProcess is null");
    assertNotNull(process.institutionProcess.getIsActive(), "institutionProcess.getIsActive() is null");

    assertNotNull(process.institutionChannelProcess, "institutionChannelProcess is null");
    assertNotNull(process.institutionChannelProcess.getIsActive(), "institutionChannelProcess.getIsActive() is null");
    assertNotNull(process.institutionChannelProcess.getWorkingStartTime(), "institutionChannelProcess.getWorkingStartTime() is null");
    assertNotNull(process.institutionChannelProcess.getWorkingFinishTime(), "institutionChannelProcess.getWorkingFinishTime() is null");

    assertNotNull(process.institutionChannel, "institutionChannel is null");
    assertNotNull(process.institutionChannel.getIsActive(), "institutionChannel.getIsActive() is null");
    assertNotNull(process.institutionChannel.getWorkingStartTime(), "institutionChannel.getWorkingStartTime() is null");
    assertNotNull(process.institutionChannel.getWorkingFinishTime(), "institutionChannel.getWorkingFinishTime() is null");

    assertNotNull(process.institutionDebtType, "institutionDebtType is null");
    assertNotNull(process.institutionDebtType.getIsActive(), "institutionDebtType.getIsActive() is null");

    // Eğer tüm bu kontrollerden geçilirse metot çalıştırılabilir
    process.beforeExecuteProcess();
}
