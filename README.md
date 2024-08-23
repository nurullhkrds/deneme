    @Override
    public DataResult<ReturnMapDefinitionDTO> getReturnMapDefinitionByReturnMapCode(String returnMapCode) {
        Optional<ReturnMapDefinition> optionalReturnMapDefinition = returnMapDefinitionRepository.findByReturnMapCode(returnMapCode);

        if (optionalReturnMapDefinition.isPresent()) {
            ReturnMapDefinitionDTO dto= returnMapDefinitionMapper.toReturnMapDefinitionDTO(optionalReturnMapDefinition.get());

            if (dto != null) {
                return new SuccessDataResult<>(ResultConstant.DATA_RETRIEVED.getMessage(),dto, 200);
            } else {
                return new ErrorDataResult<>(ResultConstant.CONVERSION_FAILED.getMessage(), null, 500);
            }
        } else {
            return new ErrorDataResult<>(ResultConstant.RECORD_NOT_FOUND.getMessage(), null, 200);
        }
    }


SELECT *
  FROM bill.institution
 where id in
       (SELECT institution_id
          FROM bill.institution_adapter
         where adapter_id in
               (SELECT ADAPTER_ID
                  FROM bill.adapter_service
                 where SERVICE_ID in
                       (SELECT id
                          FROM bill.service
                         where return_map_code = 'TAHSILAT_ITO_ALL')))



  @Query("SELECT r FROM ReturnMapDefinition r WHERE LOWER(r.returnMapCode) = LOWER(:returnMapCode)")
    Optional<ReturnMapDefinition> findByReturnMapCode(@Param("returnMapCode") String returnMapCode);
