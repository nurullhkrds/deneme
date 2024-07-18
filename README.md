public class ReverseProvisionFactoryTest {

    @Mock
    private ReverseProvisionService reverseProvisionService1;

    @Mock
    private ReverseProvisionService reverseProvisionService2;

    @InjectMocks
    private ReverseProvisionFactory reverseProvisionFactory;

    private EnumProvisionType provisionType1 = EnumProvisionType.CARD;
    private EnumProvisionType provisionType2 = EnumProvisionType.ACCOUNT;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
        when(reverseProvisionService1.getProvisionType()).thenReturn(provisionType1);
        when(reverseProvisionService2.getProvisionType()).thenReturn(provisionType2);
    }

    @Test
    public void testGetReverseProvisionService_ValidProvisionType1() {
        ReverseProvisionService service = reverseProvisionFactory.getReverseProvisionService(provisionType1);
        assertNotNull(service);
        assertEquals(reverseProvisionService1, service);
    }

    @Test
    public void testGetReverseProvisionService_ValidProvisionType2() {
        ReverseProvisionService service = reverseProvisionFactory.getReverseProvisionService(provisionType2);
        assertNotNull(service);
        assertEquals(reverseProvisionService2, service);
    }

    @Test
    public void testGetReverseProvisionService_InvalidProvisionType() {
        assertThrows(IllegalArgumentException.class, () -> {
            reverseProvisionFactory.getReverseProvisionService(EnumProvisionType.CARD);
        });
    }
}
