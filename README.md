   @BeforeEach
    public void setUp() {
        // Initialize the hmnMicroPaymentMap with necessary values
        BillTransactionConstant.hmnMicroPaymentMap = new HashMap<>();
        BillTransactionConstant.hmnMicroPaymentMap.put("CARD", EnumPaymentMethod.CARD.getValue());
    }
