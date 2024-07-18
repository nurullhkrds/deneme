@Transactional
    public void createReturnMap(CreateReturnMapRequest request) {
        ReturnMap returnMap = new ReturnMap();
        returnMap.setReturnMapCode(request.getReturnMapCode());
        returnMap.setInstitutionReturnCode(request.getInstitutionReturnCode());
        returnMap.setInstitutionReturnText(request.getInstitutionReturnText());
        returnMap.setBankReturnCode(request.getBankReturnCode());
        returnMap.setBankReturnText(request.getBankReturnText());
        returnMap.setReturnType(request.getReturnType());
        returnMap.setReversible(request.getIsReversible());
        
        returnMapRepository.save(returnMap);
    }

    @Transactional
    public void updateReturnMap(UpdateReturnMapRequest request) {
        ReturnMap returnMap = returnMapRepository.findById(request.getId())
                .orElseThrow(() -> new IllegalArgumentException("ReturnMap not found with id: " + request.getId()));

        returnMap.setReturnMapCode(request.getReturnMapCode());
        returnMap.setInstitutionReturnCode(request.getInstitutionReturnCode());
        returnMap.setInstitutionReturnText(request.getInstitutionReturnText());
        returnMap.setBankReturnCode(request.getBankReturnCode());
        returnMap.setBankReturnText(request.getBankReturnText());
        returnMap.setReturnType(request.getReturnType());
        returnMap.setReversible(request.getIsReversible());

        returnMapRepository.save(returnMap);
    }

    @Transactional
    public void deleteReturnMap(Long id) {
        returnMapRepository.deleteById(id);
    }

    @Transactional
    public void deleteReturnMaps(List<Long> ids) {
        List<ReturnMap> returnMapsToDelete = returnMapRepository.findAllById(ids);
        returnMapRepository.deleteInBatch(returnMapsToDelete);
    }

    @Transactional
    public void copyReturnMaps(List<Long> ids, String copySuffix) {
        List<ReturnMap> returnMapsToCopy = returnMapRepository.findAllById(ids);
        List<ReturnMap> copiedReturnMaps = new ArrayList<>();

        for (ReturnMap original : returnMapsToCopy) {
            ReturnMap copied = new ReturnMap();
            copied.setReturnMapCode(original.getReturnMapCode() + copySuffix);
            copied.setInstitutionReturnCode(original.getInstitutionReturnCode());
            copied.setInstitutionReturnText(original.getInstitutionReturnText());
            copied.setBankReturnCode(original.getBankReturnCode());
            copied.setBankReturnText(original.getBankReturnText());
            copied.setReturnType(original.getReturnType());
            copied.setReversible(original.getReversible());

            copiedReturnMaps.add(copied);
        }

        returnMapRepository.saveAll(copiedReturnMaps);
    }
