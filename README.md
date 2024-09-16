   @Test
     void testUpdateReturnMap_DefinitionNotFound() {
        UpdateReturnMapRequest request = new UpdateReturnMapRequest();
        request.setId(1L);
        request.setReturnMapDefinitionId(1L);

        DataResult<ReturnMapDefinition> definitionDataResult = new ErrorDataResult<>("error", null, 400);
        Mockito.when(returnMapDefinitionService.getReturnMapDefinitionByIdForServices(1L))
                .thenReturn(definitionDataResult);

        DataNotFoundException exception = Assertions.assertThrows(DataNotFoundException.class, () -> {
            returnMapService.updateReturnMap(request);
        });

        ExceptionData error = exception.getExceptionData();
        assertNotNull(error);
        assertEquals(400L, error.getErrorCode());
        assertEquals("ReturnMapService", error.getApplicationName());
        assertEquals(ResultConstant.RECORD_NOT_FOUND.getMessage(), error.getErrorMessage());
    }
org.opentest4j.AssertionFailedError: Unexpected exception type thrown ==> expected: <com.ykb.architecture.micro.error.exception.DataNotFoundException> but was: <java.lang.NullPointerException>
