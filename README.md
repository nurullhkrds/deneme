Product product = productRepository.findByCode(request.getProductCode())
    .orElseThrow(() -> {
        // ExceptionData nesnesini oluşturuyoruz
        ExceptionData error = new ExceptionData();
        error.setErrorCode(409L);  // Conflict durumunu belirtiyor
        error.setErrorMessage(ResultConstant.RECORD_ALREADY_EXISTS_SECOND.getMessage());
        error.setApplicationName(SERVICE_NAME);
        
        // DataConflictException ile hata fırlatıyoruz
        throw new DataConflictException(error);
    });
