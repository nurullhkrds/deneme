
public class ProvisionFactoryTest {

    @Mock
    private List<ProvisionService> provisionServices;

    @InjectMocks
    private ProvisionFactory provisionFactory;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void whenGetProvisionServiceWithValidType_thenShouldReturnService() {
        // Arrange
        ProvisionService mockProvisionService = mock(ProvisionService.class);
        EnumProvisionType validProvisionType = EnumProvisionType.CARD;

        when(provisionServices.stream()).thenReturn(Stream.of(mockProvisionService));
        when(mockProvisionService.getProvisionType()).thenReturn(validProvisionType);

        // Act
        ProvisionService result = provisionFactory.getProvisionService(validProvisionType);

        // Assert
        assertNotNull(result);
        assertEquals(mockProvisionService, result);
    }
}
