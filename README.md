@Test
void testSaveProcessLog_withLongResponseData() {
    // Arrange
    String longResponseData = "a".repeat(LoggingConstants.MAX_LOGGING_LENGHT * 2);
    processLogDTO.setResponseData1(longResponseData);

    ProcessLog processLogEntity = new ProcessLog();
    when(mapper.toEntity(any(ProcessLogDTO.class))).thenReturn(processLogEntity);
    when(repo.save(any(ProcessLog.class))).thenReturn(processLogEntity);

    // Act
    service.saveProcessLog(processLogDTO);

    // Assert
    assertEquals(LoggingConstants.MAX_LOGGING_LENGHT, processLogDTO.getResponseData1().length());
    assertEquals(LoggingConstants.MAX_LOGGING_LENGHT, processLogDTO.getResponseData2().length());
}

@Test
void testSaveProcessLog_withShortResponseData() {
    // Arrange
    String shortResponseData = "a".repeat(LoggingConstants.MAX_LOGGING_LENGHT - 10);
    processLogDTO.setResponseData1(shortResponseData);

    ProcessLog processLogEntity = new ProcessLog();
    when(mapper.toEntity(any(ProcessLogDTO.class))).thenReturn(processLogEntity);
    when(repo.save(any(ProcessLog.class))).thenReturn(processLogEntity);

    // Act
    service.saveProcessLog(processLogDTO);

    // Assert
    assertEquals(shortResponseData, processLogDTO.getResponseData1());
    assertNull(processLogDTO.getResponseData2());
}
