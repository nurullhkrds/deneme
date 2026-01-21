protected RequestContext requestContext;

@BeforeEach
void setUp() {
    if (springUtilMock != null) {
        springUtilMock.close();
        springUtilMock = null;
    }

    institutionRelationService = Mockito.mock(InstitutionRelationService.class);
    entityManager = Mockito.mock(EntityManager.class);
    requestContext = Mockito.mock(RequestContext.class);

    springUtilMock = Mockito.mockStatic(SpringUtil.class);

    springUtilMock.when(() -> SpringUtil.getBean(InstitutionRelationService.class))
            .thenReturn(institutionRelationService);

    springUtilMock.when(() -> SpringUtil.getBean(EntityManager.class))
            .thenReturn(entityManager);

    // ✅ BUNU EKLE: RequestContext bean gibi dönsün
    springUtilMock.when(() -> SpringUtil.getBean(RequestContext.class))
            .thenReturn(requestContext);

    // SequenceUtil için stub (aynı kalsın)
    Query query = Mockito.mock(Query.class);
    Mockito.when(entityManager.createNativeQuery(Mockito.anyString()))
            .thenReturn(query);
    Mockito.when(query.getSingleResult())
            .thenReturn(java.math.BigDecimal.valueOf(1L));
}
