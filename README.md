    @Test
    public void testCreateReturnMap_DefinitionNotFound() throws DataNotFoundException, DataConflictException {
        CreateReturnMapRequest request = new CreateReturnMapRequest();
        request.setReturnMapDefinitionId(1L);

        DataResult<ReturnMapDefinition> definitionDataResult = new ErrorDataResult<>("error",null,400);

        Mockito.when(returnMapDefinitionService.getReturnMapDefinitionByIdForServices(1L))
                .thenReturn(definitionDataResult);

        returnMapService.createReturnMap(request); // Should throw DataNotFoundException
    }

com.ykb.architecture.micro.error.exception.DataNotFoundException: Kayıt bulunumadı
