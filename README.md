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
        requestDTO.setProductCode("testProductCode");
        requestDTO.setInstitutionCode("testInstitutionCode");
        requestDTO.setBillProvisionId("testBillProvisionId");
        requestDTO.setPaymentMethod("testPaymentMethod");
        requestDTO.setAccountNo("testAccountNo");
        requestDTO.setAccountBranchCode("testAccountBranchCode");
        requestDTO.setCardNo("testCardNo");
        requestDTO.setPaymentAmount(new BigDecimal("100.00"));
        requestDTO.setPaymentCurrency("USD");

        GetBillPaymentExpenseResponseDTO responseDTO = new GetBillPaymentExpenseResponseDTO();
        // Fill responseDTO fields as needed
        responseDTO.setCommissionAmount(new BigDecimal("10.00")); // Example value

        when(paymentFacade.getBillPaymentExpense(any(GetBillPaymentExpenseRequestDTO.class))).thenReturn(responseDTO);
        doNothing().when(requestContext).setChannelSessionId(any(String.class));
        doNothing().when(requestContext).setChannelTransactionId(any(String.class));
        doNothing().when(requestContext).setAgentCode(any(String.class));
        doNothing().when(requestContext).setChannelCode(any(String.class));
        doNothing().when(requestContext).setOperatingBranchCode(any(String.class));

        mockMvc.perform(get("/adkBillPayment/getBillPaymentExpense")
                .param("agentCode", requestDTO.getAgentCode())
                .param("channelCode", requestDTO.getChannelCode())
                .param("operatingBranchCode", requestDTO.getOperatingBranchCode())
                .param("productCode", requestDTO.getProductCode())
                .param("institutionCode", requestDTO.getInstitutionCode())
                .param("billProvisionId", requestDTO.getBillProvisionId())
                .param("paymentMethod", requestDTO.getPaymentMethod())
                .param("accountNo", requestDTO.getAccountNo())
                .param("accountBranchCode", requestDTO.getAccountBranchCode())
                .param("cardNo", requestDTO.getCardNo())
                .param("paymentAmount", requestDTO.getPaymentAmount().toString())
                .param("paymentCurrency", requestDTO.getPaymentCurrency())
                .header("x-trace-id", "trace-id")
                .header("x-session-id", "session-id")
                .contentType(MediaType.APPLICATION_JSON))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.commissionAmount").value("10.00")); // replace with actual fields in responseDTO

        verify(paymentFacade).getBillPaymentExpense(any(GetBillPaymentExpenseRequestDTO.class));
    }
}
