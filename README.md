@Test
void testSaveProcessLog_withValidData() {
    // Arrange
    ProcessLog processLogEntity = new ProcessLog();
    when(mapper.toEntity(any(ProcessLogDTO.class))).thenReturn(processLogEntity);
    when(repo.save(any(ProcessLog.class))).thenReturn(processLogEntity);

    // Act
    service.saveProcessLog(processLogDTO);

    // Assert
    assertNotNull(processLogDTO.getElapsedTime());
    assertTrue(processLogDTO.getRequestData().length() <= LoggingConstants.MAX_LOGGING_LENGHT);
    
    if (processLogDTO.getResponseData2() != null) {
        assertTrue(processLogDTO.getResponseData2().length() <= LoggingConstants.MAX_LOGGING_LENGHT);
    } else {
        assertNull(processLogDTO.getResponseData2());
    }
}
