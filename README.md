@ExtendWith(MockitoExtension.class)
public class PaymentAdkControllerTest {

    @Mock
    private PaymentService paymentService;

    @Mock
    private PaymentFacade paymentFacade;

    @Mock
    private InstitutionBarcodeService institutionBarcodeService;

    @Mock
    private RequestContext requestContext;

    @InjectMocks
    private PaymentAdkController paymentAdkController;

    @Autowired
    private MockMvc mockMvc;

    @BeforeEach
    public void setup() {
        mockMvc = MockMvcBuilders.standaloneSetup(paymentAdkController)
                .build();
    }

    @Test
    public void testGetBillPaymentExpense() throws Exception {
        GetBillPaymentExpenseRequestDTO requestDTO = new GetBillPaymentExpenseRequestDTO();
        // Fill the requestDTO with valid data as per your validation requirements
        requestDTO.setAgentCode("testAgent");
        requestDTO.setChannelCode("testChannel");
        requestDTO.setOperatingBranchCode("testBranch");
        // Add other required fields

        GetBillPaymentExpenseResponseDTO responseDTO = new GetBillPaymentExpenseResponseDTO();
        // Fill responseDTO fields as needed

        when(paymentFacade.getBillPaymentExpense(any(GetBillPaymentExpenseRequestDTO.class))).thenReturn(responseDTO);
        doNothing().when(requestContext).setChannelSessionId(any(String.class));
        doNothing().when(requestContext).setChannelTransactionId(any(String.class));
        doNothing().when(requestContext).setAgentCode(any(String.class));
        doNothing().when(requestContext).setChannelCode(any(String.class));
        doNothing().when(requestContext).setOperatingBranchCode(any(String.class));

        mockMvc.perform(get("/adkBillPayment/getBillPaymentExpense")
                .param("agentCode", "testAgent")
                .param("channelCode", "testChannel")
                .param("operatingBranchCode", "testBranch")
                .header("x-trace-id", "trace-id")
                .header("x-session-id", "session-id")
                .contentType(MediaType.APPLICATION_JSON))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.someField").value("expectedValue")); // replace with actual fields in responseDTO

        verify(paymentFacade).getBillPaymentExpense(any(GetBillPaymentExpenseRequestDTO.class));
    }
}
