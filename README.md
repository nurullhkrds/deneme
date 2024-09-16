  @Test
     void testUpdateReturnMap_Success() throws DataNotFoundException, DataConflictException {
        UpdateReturnMapRequest request = new UpdateReturnMapRequest();
        request.setId(1L);
        request.setReturnMapDefinitionId(1L);
        request.setInstitutionReturnCode("123");
        request.setInstitutionReturnText("Text");
        request.setBankReturnCode("456");
        request.setBankReturnText("Bank Text");
        request.setIsReversible(true);
        request.setReturnType("SUCCESS");
        request.setUpdateUser("User");

        ReturnMapDefinition returnMapDefinition = new ReturnMapDefinition();
        returnMapDefinition.setReturnMapCode("MapCode");
        DataResult<ReturnMapDefinition> definitionDataResult = new ErrorDataResult<>("success", returnMapDefinition, 400);
        Mockito.when(returnMapDefinitionService.getReturnMapDefinitionByIdForServices(1L))
                .thenReturn(definitionDataResult);

        ReturnMap returnMap = new ReturnMap();
        returnMap.setId(1L);
        Mockito.when(returnMapRepository.findById(1L)).thenReturn(Optional.of(returnMap));

        Mockito.when(returnMapRepository.save(any(ReturnMap.class))).thenReturn(returnMap);

        ReturnMapDTO returnMapDTO = new ReturnMapDTO();
        Mockito.when(returnMapMapper.toReturnMapDTO(returnMap)).thenReturn(returnMapDTO);

        DataResult<ReturnMapDTO> result = returnMapService.updateReturnMap(request);

        assertTrue(result.isSuccess());
        assertEquals(200, result.getStatusCode());
        assertNotNull(result.getData());
    }



com.ykb.architecture.micro.error.exception.DataNotFoundException: Kayıt bulunumadı
