     ExceptionData error=new ExceptionData();
            error.setErrorCode(400L);
            error.setErrorMessage(ResultConstant.RECORD_NOT_FOUND.getMessage());
            error.setApplicationName("ReturnMapService");
Define a constant instead of duplicating this literal "ReturnMapService" 4 times.
            throw new DataNotFoundException(error);
        }
        Optional<ReturnMap> existingReturnMap = returnMapRepository.findByReturnMapDefinitionIdAndInstitutionReturnCode(
                request.getReturnMapDefinitionId(), request.getInstitutionReturnCode());
        if (existingReturnMap.isPresent()) {
            ExceptionData error=new ExceptionData();
            error.setErrorCode(409L);
            error.setErrorMessage(ResultConstant.RECORD_ALREADY_EXISTS_SECOND.getMessage());
            error.setApplicationName("ReturnMapService");
            throw  new DataConflictException(error) ;
        }
        ReturnMap returnMap = new ReturnMap();
        returnMap.setReturnMapCode(definitionDataResult.getData().getReturnMapCode());
        returnMap.setInstitutionReturnCode(request.getInstitutionReturnCode());


        if (request.getReturnMapDefinitionId() == null && !definitionDataResult.isSuccess()) {
            ExceptionData error=new ExceptionData();
            error.setErrorCode(400L);
            error.setErrorMessage(ResultConstant.RECORD_NOT_FOUND.getMessage());
            error.setApplicationName("ReturnMapService");
            throw new DataNotFoundException(error);
        }
        Optional<ReturnMap> existingReturnMap = returnMapRepository.findByReturnMapDefinitionIdAndInstitutionReturnCode(
                request.getReturnMapDefinitionId(), request.getInstitutionReturnCode());
        if (existingReturnMap.isPresent() && !existingReturnMap.get().getId().equals(request.getId())) {
            ExceptionData error=new ExceptionData();
            error.setErrorCode(409L);
            error.setErrorMessage(ResultConstant.RECORD_ALREADY_EXISTS_SECOND.getMessage());
            error.setApplicationName("ReturnMapService");
            throw  new DataConflictException(error) ;
        }
