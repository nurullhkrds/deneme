   @Transactional
    public void deleteReturnMaps(List<Long> ids) {
        List<ReturnMap> returnMapsToDelete = returnMapRepository.findAllById(ids);
        returnMapRepository.deleteAll(returnMapsToDelete);
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
            copied.setIsReversible(original.getIsReversible());

            copiedReturnMaps.add(copied);
        }

        returnMapRepository.saveAll(copiedReturnMaps);
    }
