public class CommonIntegrationTest {

    private MockedStatic<SpringUtil> springUtilMock;

    // SpringUtil.getBean ile çekilenler
    private InstitutionRelationService institutionRelationService;
    private EntityManager entityManager;

    @BeforeEach
    void mockSpringUtilBeans() {
        institutionRelationService = Mockito.mock(InstitutionRelationService.class);
        entityManager = Mockito.mock(EntityManager.class);

        // SpringUtil statik mock
        springUtilMock = Mockito.mockStatic(SpringUtil.class);

        // 1) InstitutionRelationService bean gibi dönsün
        springUtilMock.when(() -> SpringUtil.getBean(InstitutionRelationService.class))
                .thenReturn(institutionRelationService);

        // 2) EntityManager bean gibi dönsün
        springUtilMock.when(() -> SpringUtil.getBean(EntityManager.class))
                .thenReturn(entityManager);

        // 3) SequenceUtil muhtemelen native query ile nextval çekiyor -> onu da stubla
        Query query = Mockito.mock(Query.class);
        Mockito.when(entityManager.createNativeQuery(Mockito.anyString()))
                .thenReturn(query);

        // nextval dönüş tipi bazen BigDecimal olur; safest:
        Mockito.when(query.getSingleResult())
                .thenReturn(BigDecimal.valueOf(1L));
    }

    @AfterEach
    void closeStaticMock() {
        if (springUtilMock != null) {
            springUtilMock.close();
        }
    }

    // ---------------------------------------------------------
    // SENİN METOT: sadece 1 satırı düzeltiyoruz (lenient + mock field)
    // ---------------------------------------------------------
    private InstitutionServiceDTO getInstitutionServiceDTO(String serviceType) {
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
                    Mockito.when(tokenService.getTokenByDefinitionKey(org.mockito.ArgumentMatchers.anyString()))
                            .thenReturn(tokenDTO);
                } else {
                    Mockito.when(tokenService.getCurrentLatestActiveToken(org.mockito.ArgumentMatchers.anyString()))
                            .thenReturn(tokenDTO);

                    if (org.springframework.util.StringUtils.isEmpty(token)) {
                        Mockito.when(tokenService.saveToken(
                                        org.mockito.ArgumentMatchers.anyString(),
                                        org.mockito.ArgumentMatchers.anyString()))
                                .thenReturn(tokenDTO);
                    }
                }
            } catch (MicroException e) {
                e.printStackTrace();
            }
        }

        InstitutionDTO institutionDTO = new InstitutionDTO();
        institutionDTO.setParamaters(paramsList);

        // ✅ ÖNEMLİ: SpringUtil.getBean(...) üzerinden gitme.
        // Zaten SpringUtil mock'u bu field'ı döndürüyor.
        // ✅ Ayrıca lenient: kullanılmayan testte UnnecessaryStubbingException atmasın.
        Mockito.lenient().when(institutionRelationService.getInstitution(
                        org.mockito.ArgumentMatchers.anyString(),
                        org.mockito.ArgumentMatchers.anyString()))
                .thenReturn(institutionDTO);

        return t;
    }
}
