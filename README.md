    @Override
    public List<InstitutionChnnlPymMthdAccDTO> getAllInstitutionChannelPymMethodsAcc() {
        List<InstitutionChnnlPymMthdAcc> institutionChnnlPymMthdAccs = institutionChnnlPymMthdAccRepository.findAll();
        return institutionChnnlPymMthdAccMapper.toDTOList(institutionChnnlPymMthdAccs);
    }
