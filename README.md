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
    void setUp() {
        when(reverseProvisionService1.getProvisionType()).thenReturn(provisionType1);
        when(reverseProvisionService2.getProvisionType()).thenReturn(provisionType2);

        List<ReverseProvisionService> reverseProvisionServices = Arrays.asList(reverseProvisionService1, reverseProvisionService2);
        new ReverseProvisionFactory(reverseProvisionServices);  // Initialize the factory with the mocked services
    }

    @Test
    void testGetReverseProvisionService_Success() {
        assertEquals(reverseProvisionService1, reverseProvisionFactory.getReverseProvisionService(provisionType1));
        assertEquals(reverseProvisionService2, reverseProvisionFactory.getReverseProvisionService(provisionType2));
    }

    @Test
    void testGetReverseProvisionService_Failure() {
        assertThrows(IllegalArgumentException.class, () -> reverseProvisionFactory.getReverseProvisionService(EnumProvisionType.UNKNOWN));
    }
}
