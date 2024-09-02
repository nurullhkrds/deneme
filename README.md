    @Test
    void testSaveProcessLog_withValidData() {
        // Arrange
        ProcessLog processLogEntity = new ProcessLog();
        when(mapper.toEntity(any(ProcessLogDTO.class))).thenReturn(processLogEntity);
        when(repo.save(any(ProcessLog.class))).thenReturn(processLogEntity);

        // Act
        service.saveProcessLog(processLogDTO);


        assertNotNull(processLogDTO.getElapsedTime());
        assertTrue(processLogDTO.getRequestData().length() <= LoggingConstants.MAX_LOGGING_LENGHT);
        assertTrue(processLogDTO.getResponseData1().length() <= LoggingConstants.MAX_LOGGING_LENGHT);
        assertTrue(processLogDTO.getResponseData2().length() <= LoggingConstants.MAX_LOGGING_LENGHT);
    }

java.lang.NullPointerException: Cannot invoke "String.length()" because the return value of "com.ykb.payments.bill.common.logging.dto.ProcessLogDTO.getResponseData2()" is null
