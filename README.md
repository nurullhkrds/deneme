    @Test
    public void testCreateReturnMap_Conflict() throws DataNotFoundException, DataConflictException {
        CreateReturnMapRequest request = new CreateReturnMapRequest();
        request.setReturnMapDefinitionId(1L);
        request.setInstitutionReturnCode("123");

        ReturnMap returnMap = new ReturnMap();

        Mockito.when(returnMapRepository.findByReturnMapDefinitionIdAndInstitutionReturnCode(1L, "123"))
                .thenReturn(Optional.of(returnMap));

        returnMapService.createReturnMap(request);
    }
java.lang.NullPointerException: Cannot invoke "com.ykb.payments.bill.transaction.adapter.core.utilities.DataResult.isSuccess()" because "definitionDataResult" is null
