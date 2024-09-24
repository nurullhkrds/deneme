@Test
void testEliminateBills_NoBillsFound() throws BillException {
    // Empty queriedBillDTOList
    queriedBillDTOList = new ArrayList<>();
    queryBillsProcess.setQueriedBillDTOList(queriedBillDTOList);

    // Execute the process
    queryBillsProcess.executeProcess();

    // Validate that error is set to BILL_NOT_FOUND
    assertEquals(EnumBillResult.BILL_NOT_FOUND, queryBillsProcess.getExecutionOutput().getResult());
}

@Test
void testEliminateBills_MatchInHarmoniPaidBills() throws BillException {
    // Set up queried bills
    QueriedBillDTO queriedBill = new QueriedBillDTO();
    queriedBill.setBillNo("12345");
    queriedBill.setBillDueDate(LocalDate.now());
    queriedBillDTOList = List.of(queriedBill);

    // Set up harmoniPaidBills with a matching bill
    HmnPaidBillDTO harmoniPaidBill = new HmnPaidBillDTO();
    harmoniPaidBill.setBillNo("12345");
    harmoniPaidBill.setBillDueDate(new Date()); // Matching the same bill date
    ResponseGetCustomerPaidBillList harmoniPaidBills = new ResponseGetCustomerPaidBillList();
    harmoniPaidBills.setBillDTOList(List.of(harmoniPaidBill));

    when(billPaymentRestFacade.getCustomerPaidBillList(anyString(), anyString(), anyString()))
            .thenReturn(harmoniPaidBills);

    // No bills in mikroPaidBillList
    when(paymentRepository.findPaidBillList(anyString(), anyLong(), anyString())).thenReturn(new ArrayList<>());

    queryBillsProcess.executeProcess();

    // Validate that queriedBillDTOList is filtered and empty
    assertTrue(queryBillsProcess.getQueriedBillDTOList().isEmpty());
    assertEquals(EnumBillResult.BILL_NOT_FOUND, queryBillsProcess.getExecutionOutput().getResult());
}

@Test
void testEliminateBills_MatchInMikroPaidBills() throws BillException {
    // Set up queried bills
    QueriedBillDTO queriedBill = new QueriedBillDTO();
    queriedBill.setBillNo("67890");
    queriedBill.setBillDueDate(LocalDate.now());
    queriedBillDTOList = List.of(queriedBill);

    // No bills in harmoniPaidBills
    when(billPaymentRestFacade.getCustomerPaidBillList(anyString(), anyString(), anyString()))
            .thenReturn(new ResponseGetCustomerPaidBillList());

    // Set up mikroPaidBillList with a matching bill
    PaymentDTO mikroPaidBill = new PaymentDTO();
    mikroPaidBill.setBillNo("67890");
    mikroPaidBill.setBillDueDate(LocalDate.now());
    when(paymentRepository.findPaidBillList(anyString(), anyLong(), anyString()))
            .thenReturn(List.of(mikroPaidBill));

    queryBillsProcess.executeProcess();

    // Validate that queriedBillDTOList is filtered and empty
    assertTrue(queryBillsProcess.getQueriedBillDTOList().isEmpty());
    assertEquals(EnumBillResult.BILL_NOT_FOUND, queryBillsProcess.getExecutionOutput().getResult());
}

@Test
void testEliminateBills_NoMatchesFound() throws BillException {
    // Set up queried bills
    QueriedBillDTO queriedBill = new QueriedBillDTO();
    queriedBill.setBillNo("12345");
    queriedBill.setBillDueDate(LocalDate.now());
    queriedBillDTOList = List.of(queriedBill);

    // No matching bills in harmoniPaidBills or mikroPaidBills
    when(billPaymentRestFacade.getCustomerPaidBillList(anyString(), anyString(), anyString()))
            .thenReturn(new ResponseGetCustomerPaidBillList());

    when(paymentRepository.findPaidBillList(anyString(), anyLong(), anyString()))
            .thenReturn(new ArrayList<>());

    queryBillsProcess.executeProcess();

    // Validate that queriedBillDTOList is not empty
    assertFalse(queryBillsProcess.getQueriedBillDTOList().isEmpty());
    assertNull(queryBillsProcess.getExecutionOutput().getResult()); // No error
}
