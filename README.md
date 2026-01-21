public abstract class CommonIntegrationTest {

    protected MockedStatic<SpringUtil> springUtilMock;

    protected InstitutionRelationService institutionRelationService;
    protected EntityManager entityManager;

    @BeforeEach
    void setUp() {
        // 1) Eğer önceki testten kapanmamışsa, önce kapat (garanti)
        if (springUtilMock != null) {
            springUtilMock.close();
            springUtilMock = null;
        }

        institutionRelationService = Mockito.mock(InstitutionRelationService.class);
        entityManager = Mockito.mock(EntityManager.class);

        // 2) Static mock'u 1 kez aç
        springUtilMock = Mockito.mockStatic(SpringUtil.class);

        springUtilMock.when(() -> SpringUtil.getBean(InstitutionRelationService.class))
                .thenReturn(institutionRelationService);

        springUtilMock.when(() -> SpringUtil.getBean(EntityManager.class))
                .thenReturn(entityManager);

        // SequenceUtil için güvenli stub
        Query query = Mockito.mock(Query.class);
        Mockito.when(entityManager.createNativeQuery(Mockito.anyString()))
                .thenReturn(query);

        Mockito.when(query.getSingleResult())
                .thenReturn(BigDecimal.valueOf(1L));
    }

    @AfterEach
    void tearDown() {
        if (springUtilMock != null) {
            springUtilMock.close();
            springUtilMock = null;
        }
    }

    protected InstitutionServiceDTO getInstitutionServiceDTO(String serviceType) {
        String url = virtualServiceUrl != null ? virtualServiceUrl : virtualServiceUrlList.get(serviceType);
        InstitutionServiceDTO t = new InstitutionServiceDTO();
        t.setUrl(url);
        t.setServiceType(serviceType);
        t.setServiceTimeoutDTO(new ServiceTimeoutDTO(connectionTimeout, readTimeout));
        t.setUserName(userName);
        t.setPword(password);
        t.setParamaters(paramsList);

        adapter.setInstitutionService(t);

        // ... tokenService stubların aynı kalsın ...

        InstitutionDTO institutionDTO = new InstitutionDTO();
        institutionDTO.setParamaters(paramsList);

        // UnnecessaryStubbingException gelmesin diye lenient
        Mockito.lenient().when(institutionRelationService.getInstitution(
                        Mockito.anyString(), Mockito.anyString()))
                .thenReturn(institutionDTO);

        return t;
    }
}
