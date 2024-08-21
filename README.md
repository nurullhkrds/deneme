 @Override
    public DataResult<BaseRetrunMapDTO> createBaseReturnMap(CreateBaseReturnMapRequest request) {

        Optional<BaseReturnMap> existingReturnMap = baseReturnMapRepository.findByReturnMapCode(request.getReturnMapCode());

        if (existingReturnMap.isPresent()) {
            return new ErrorDataResult<>("Bu kod ile zaten bir kayıt mevcut.",null,400);
        }

        BaseReturnMap newReturnMap = new BaseReturnMap();
        newReturnMap.setReturnMapCode(request.getReturnMapCode());
        newReturnMap.setIsActive(request.getIsActive());

        // Kayıt veritabanına eklenir
        BaseReturnMap savedData=baseReturnMapRepository.save(newReturnMap);
        
        BaseRetrunMapDTO dto=baseReturnMapMapper.toBaseReturnMapDTO(savedData);
        
        if (dto == null){
            return new ErrorDataResult<>()
        }
        
        return new SuccessDataResult<>();
    }
