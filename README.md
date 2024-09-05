
    @Override
    public Result deleteReturnMapDefinitions(DeleteIdsRequest request) {
        try{
            List<ReturnMapDefinition> returnMapsToDelete = returnMapDefinitionRepository.findAllById(request.getIds());
            
            return new SuccessResult(ResultConstant.SUCCESSFULLY_DELETED.getMessage(), 200);
        }
        catch (Exception e){
            return new ErrorResult("Error ! "+e.getMessage(),400);
        }
    }
