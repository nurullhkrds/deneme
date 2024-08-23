 @Override
    public Result deleteReturnMapDefinitions(DeleteIdsRequest request) {
        try{
            List<ReturnMapDefinition> returnMapsToDelete = returnMapDefinitionRepository.findAllById(request.getIds());
            returnMapDefinitionRepository.deleteAll(returnMapsToDelete);
            return new SuccessResult(ResultConstant.SUCCESSFULLY_DELETED.getMessage(), 200);
        }
        catch (Exception e){
            return new ErrorResult("Error ! "+e.getMessage(),400);
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
