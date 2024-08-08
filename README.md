@Test
void testGetCustomerPaidBillList() throws MicroException {
    GetCustomerPaidBillListRequest request = new GetCustomerPaidBillListRequest();
    request.setCustomerNo("12345");
    request.setChannelCode("channelCode");
    request.setProductCode("productCode");

    List<PaidBillResponseWebDTO> microBillList = new ArrayList<>();
    List<PaidBillResponseWebDTO> harmoniBillList = new ArrayList<>();

    when(paymentRepository.findCustomerPaidBillList(any(LocalDate.class), eq("12345"), eq("PAID"), eq("productCode")))
        .thenReturn(new ArrayList<>());
    
    when(billPaymentRestFacade.getCustomerPaidBillList(eq("12345")))
        .thenReturn(new ResponseGetCustomerPaidBillList(SUCCESS, new ArrayList<>()));

    when(channelService.findChannelByChannelCode(anyString()))
        .thenReturn(new ChannelDTO());

    GetCustomerPaidBillListResponse response = paymentService.getCustomerPaidBillList(request);

    assertNotNull(response);
    assertEquals(0, response.getBillList().size());

    // Ek testler ve doğrulamalar
}

@Test
void testParseSubscriberNoIntoParts() {
    ParseSubscriberNoIntoPartsRequest request = new ParseSubscriberNoIntoPartsRequest();
    request.setSubscriberNo("1234567890");
    request.setProductCode("productCode");
    request.setInstitutionCode("institutionCode");

    List<InstitutionUserIntfDTO> userInterfaceList = new ArrayList<>();
    when(institutionUserIntService.getDefaultUserInterface(eq("productCode"), eq("institutionCode")))
        .thenReturn(userInterfaceList);

    ParseSubscriberNoIntoPartsResponse response = paymentService.parseSubscriberNoIntoParts(request);

    assertNotNull(response);
    assertEquals(0, response.getSubsrciberNoPartResponseWebDTO().size());

    // Ek testler ve doğrulamalar
}

@Test
void testGetMicroBillList() throws MicroException {
    GetCustomerPaidBillListRequest request = new GetCustomerPaidBillListRequest();
    request.setCustomerNo("12345");
    request.setChannelCode("channelCode");
    request.setProductCode("productCode");

    List<Payment> paymentList = new ArrayList<>();
    when(paymentRepository.findCustomerPaidBillList(any(LocalDate.class), eq("12345"), eq("PAID"), eq("productCode")))
        .thenReturn(paymentList);

    when(channelService.findChannelByChannelCode(anyString()))
        .thenReturn(new ChannelDTO());

    List<HmnPaidBillDTO> result = paymentService.getMicroBillList(request);

    assertNotNull(result);
    assertEquals(0, result.size());

    // Ek testler ve doğrulamalar
}

