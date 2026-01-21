private InstitutionServiceDTO getInstitutionServiceDTO(String serviceType) {
    // 1) SpringUtil.getBean(...) -> mock InstitutionRelationService dönecek
    InstitutionRelationService institutionRelationService = Mockito.mock(InstitutionRelationService.class);

    // Not: try bloğunun dışına mockStatic taşma yapmayacak şekilde kullandım.
    try (MockedStatic<SpringUtil> springUtilMock = Mockito.mockStatic(SpringUtil.class)) {

        springUtilMock
                .when(() -> SpringUtil.getBean(InstitutionRelationService.class))
                .thenReturn(institutionRelationService);

        String url = virtualServiceUrl != null ? virtualServiceUrl : virtualServiceUrlList.get(serviceType);
        InstitutionServiceDTO t = new InstitutionServiceDTO();
        t.setUrl(url);
        t.setServiceType(serviceType);
        t.setServiceTimeoutDTO(new ServiceTimeoutDTO(connectionTimeout, readTimeout));
        t.setUserName(userName);
        t.setPword(password);
        t.setParamaters(paramsList);

        adapter.setInstitutionService(t);

        if (token != null) {
            TokenDefinitionDTO tokenDefinition = new TokenDefinitionDTO();
            tokenDefinition.setRunType(tokenRunType);
            tokenDefinition.setDefinitionKey("key");
            adapter.setTokenDefinition(tokenDefinition);
            try {
                TokenDTO tokenDTO = new TokenDTO();
                tokenDTO.setTokenDefinition(tokenDefinition);
                tokenDTO.setTokenData(token);
                tokenDTO.setId(1L);

                if (tokenRunType.equals(EnumTokenRunType.WITH_LOCK)) {
                    Mockito.when(tokenService.getTokenByDefinitionKey(ArgumentMatchers.anyString()))
                           .thenReturn(tokenDTO);
                } else {
                    Mockito.when(tokenService.getCurrentLatestActiveToken(ArgumentMatchers.anyString()))
                           .thenReturn(tokenDTO);

                    if (StringUtils.isEmpty(token)) {
                        Mockito.when(tokenService.saveToken(ArgumentMatchers.anyString(), ArgumentMatchers.anyString()))
                               .thenReturn(tokenDTO);
                    }
                }
            } catch (MicroException e) {
                e.printStackTrace();
            }
        }

        InstitutionDTO institutionDTO = new InstitutionDTO();
        institutionDTO.setParamaters(paramsList);

        // 2) Artık SpringUtil.getBean(...) patlamaz; mock service döner
        Mockito.when(institutionRelationService.getInstitution(
                        ArgumentMatchers.anyString(),
                        ArgumentMatchers.anyString()))
               .thenReturn(institutionDTO);

        return t;
    }
}
