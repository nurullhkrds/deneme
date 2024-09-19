    @Override
    public DataResult<ReturnMapDefinitionDTO> getReturnMapDefinitionById(Long id) throws MicroException {
        Optional<ReturnMapDefinition> returnMapDefinitionOptional=returnMapDefinitionRepository.findById(id);

        if (returnMapDefinitionOptional.isPresent()){
            ReturnMapDefinitionDTO dto= returnMapDefinitionMapper.toReturnMapDefinitionDTO(returnMapDefinitionOptional.get());
            return new SuccessDataResult<>(ResultConstant.DATA_RETRIEVED.getMessage(), dto,200);
        }
        throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.DATA_NOT_FOUND);
    }

 @Override
    public DataResult<ReturnMapDefinitionDTO> getReturnMapDefinitionByReturnMapCode(String returnMapCode) throws MicroException {
        Optional<ReturnMapDefinition> optionalReturnMapDefinition = returnMapDefinitionRepository.findByReturnMapCode(returnMapCode);

        if (optionalReturnMapDefinition.isPresent()) {
            ReturnMapDefinitionDTO dto = returnMapDefinitionMapper.toReturnMapDefinitionDTO(optionalReturnMapDefinition.get());

            List<String> institutions = returnMapDefinitionRepository.findInstitutionNamesByReturnMapCode(returnMapCode);

            if (dto != null) {
                dto.setInstitutions(institutions);
                return new SuccessDataResult<>(ResultConstant.DATA_RETRIEVED.getMessage(), dto, 200);
            } else {
                throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.CONVERSION_FAILED);
            }
        } else {
            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.DATA_NOT_FOUND);
        }
    }   

@Test
    void testGetReturnMapDefinitionById_RecordNotExists() throws MicroException {
        Long id = 1L;

        when(returnMapDefinitionRepository.findById(id)).thenReturn(Optional.empty());

        DataResult<ReturnMapDefinitionDTO> result = returnMapDefinitionService.getReturnMapDefinitionById(id);

        assertFalse(result.isSuccess());
        assertEquals(400, result.getStatusCode());
        assertNull(result.getData());
    }

  @Test
    void testGetReturnMapDefinitionByReturnMapCode_RecordNotExists() throws MicroException {
        String returnMapCode = "testCode";

        when(returnMapDefinitionRepository.findByReturnMapCode(returnMapCode)).thenReturn(Optional.empty());

        DataResult<ReturnMapDefinitionDTO> result = returnMapDefinitionService.getReturnMapDefinitionByReturnMapCode(returnMapCode);

        assertFalse(result.isSuccess());
        assertEquals(200, result.getStatusCode());
        assertNull(result.getData());
    }
com.ykb.architecture.micro.error.exception.DataNotFoundException: Veri Bulunamadı !
