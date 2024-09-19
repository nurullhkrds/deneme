  @Override
    public DataResult<ProductDTO> getProductByCode(String code) throws MicroException{


        return productRepository.findByCode(code)
                .map(new SuccessDataResult<>("",productMapper::toProductDTO,200))
                .orElseThrow(() -> new DataNotFoundException(BillExceptionsUI.ValidationExceptions.PRODUCT_NOT_FOUND));
    }
