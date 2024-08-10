@SpringBootTest
@ExtendWith(MockitoExtension.class)
@DirtiesContext(classMode = DirtiesContext.ClassMode.AFTER_EACH_TEST_METHOD)
class BillPaymentReverseProcessTest {

    @MockBean
    private PaymentUtilImpl paymentUtilImpl;

    // Diğer mock nesneleri de @MockBean ile belirtin

    @InjectMocks
    private BillPaymentReverseProcess billPaymentReverseProcess;

    // Test metodları burada
}
@AfterEach
void tearDown() {
    Mockito.reset(paymentUtilImpl, paymentService, paymentNotificationService, accountingService);
}@DirtiesContext(classMode = DirtiesContext.ClassMode.BEFORE_EACH_TEST_METHOD)
