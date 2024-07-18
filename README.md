@ExtendWith(MockitoExtension.class)
public class ReverseProvisionFactoryTest {

    @Mock
    private ReverseProvisionService reverseProvisionService1;

    @Mock
    private ReverseProvisionService reverseProvisionService2;

    private ReverseProvisionFactory reverseProvisionFactory;

    private EnumProvisionType provisionType1 = EnumProvisionType.CARD;
    private EnumProvisionType provisionType2 = EnumProvisionType.ACCOUNT;

    @BeforeEach
    public void setUp() {
        // Mock objelerin davranışlarını ayarlıyoruz
        when(reverseProvisionService1.getProvisionType()).thenReturn(provisionType1);
        when(reverseProvisionService2.getProvisionType()).thenReturn(provisionType2);

        // Mock objeleri içeren bir liste oluşturuyoruz
        List<ReverseProvisionService> services = Arrays.asList(reverseProvisionService1, reverseProvisionService2);

        // ReverseProvisionFactory'yi bu liste ile başlatıyoruz
        reverseProvisionFactory = new ReverseProvisionFactory(services);
    }

    @Test
    public void testGetReverseProvisionService_ValidProvisionType1() {
        // provisionType1 için doğru servisi alabiliyor muyuz kontrol ediyoruz
        ReverseProvisionService service = reverseProvisionFactory.getReverseProvisionService(provisionType1);
        assertNotNull(service);
        assertEquals(reverseProvisionService1, service);
    }

    @Test
    public void testGetReverseProvisionService_ValidProvisionType2() {
        // provisionType2 için doğru servisi alabiliyor muyuz kontrol ediyoruz
        ReverseProvisionService service = reverseProvisionFactory.getReverseProvisionService(provisionType2);
        assertNotNull(service);
        assertEquals(reverseProvisionService2, service);
    }

    @Test
    public void testGetReverseProvisionService_InvalidProvisionType() {
        // Geçersiz bir provision type için istisna fırlatılıyor mu kontrol ediyoruz
        assertThrows(IllegalArgumentException.class, () -> {
            reverseProvisionFactory.getReverseProvisionService(EnumProvisionType.INVALID); // EnumProvisionType içinde INVALID olduğundan emin olun
        });
    }
}
