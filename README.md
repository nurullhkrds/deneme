@SpringBootTest
@ExtendWith(MockitoExtension.class)
public class ReverseProvisionFactoryTest {

    @Mock
    private ReverseProvisionService reverseProvisionService1;
    
    @Mock
    private ReverseProvisionService reverseProvisionService2;

    @InjectMocks
    private ReverseProvisionFactory reverseProvisionFactory;

    private EnumProvisionType provisionType1 = EnumProvisionType.TYPE1;
    private EnumProvisionType provisionType2 = EnumProvisionType.TYPE2;

    @BeforeEach
    public void setUp() {
        when(reverseProvisionService1.getProvisionType()).thenReturn(provisionType1);
        when(reverseProvisionService2.getProvisionType()).thenReturn(provisionType2);

        List<ReverseProvisionService> services = Arrays.asList(reverseProvisionService1, reverseProvisionService2);
        reverseProvisionFactory = new ReverseProvisionFactory(services);
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
            reverseProvisionFactory.getReverseProvisionService(EnumProvisionType.UNKNOWN);
        });
    }
}
