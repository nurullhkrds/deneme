  @Transactional
    public Result copyReturnMaps(CopyForIdsAndDataRequest request) {
        try{
            List<ReturnMap> returnMapsToCopy = returnMapRepository.findAllById(request.getIds());
            List<ReturnMap> copiedReturnMaps = new ArrayList<>();

            for (ReturnMap original : returnMapsToCopy) {
                ReturnMap copied = new ReturnMap();
                copied.setReturnMapCode(request.getReturnMapCode());
                copied.setInstitutionReturnCode(original.getInstitutionReturnCode());
                copied.setInstitutionReturnText(original.getInstitutionReturnText());
                copied.setBankReturnCode(original.getBankReturnCode());
                copied.setBankReturnText(original.getBankReturnText());
                copied.setReturnType(original.getReturnType());
                copied.setIsReversible(original.getIsReversible());
                copiedReturnMaps.add(copied);
            }

            returnMapRepository.saveAll(copiedReturnMaps);
            return new SuccessResult("copy successful",200);

        }
        catch (Exception e){
            return new ErrorResult("Error! "+e.getMessage(),400);
        }

    }
