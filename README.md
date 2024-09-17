  @Override
    public DataResult<AdapterInstitutionDTO> createInstitution(CreateInstitutionRequest request) throws DataNotFoundException {
        Product product = productRepository.findByCode(request.getProductCode())
                .orElseThrow(() -> {
                    ExceptionData error = new ExceptionData();
                    error.setErrorCode(409L);  
                    error.setErrorMessage(ResultConstant.PRODUCT_NOT_FOUND.getMessage());
                    error.setApplicationName(SERVICE_NAME);
                    return new DataNotFoundException(error);
                });
