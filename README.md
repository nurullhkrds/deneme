    @Transactional
    public Result deleteReturnMaps(DeleteIdsRequest request) {
        try{
            List<ReturnMap> returnMapsToDelete = returnMapRepository.findAllById(request.getIds());
            returnMapRepository.deleteAll(returnMapsToDelete);
            return new SuccessResult("ReturnMaps deleted",200);
        }
        catch (Exception e){
            return new ErrorResult("Error ! "+e.getMessage(),400);
        }
    }
