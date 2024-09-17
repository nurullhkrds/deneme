        Product product = productRepository.findByCode(request.getProductCode())
        ExceptionData error=new ExceptionData();
        error.setErrorCode(409L);
        error.setErrorMessage(ResultConstant.RECORD_ALREADY_EXISTS_SECOND.getMessage());
        error.setApplicationName(SERVICE_NAME);
        throw  new DataConflictException(error) ;
                .orElseThrow(() -> new DataNotFoundException();
