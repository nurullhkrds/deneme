   @Test
    public void testGetCustomerPaidBillList() throws MicroException {
        // Given
        GetCustomerPaidBillListRequest request = new GetCustomerPaidBillListRequest();
        request.setCustomerNo("12345");
        request.setProductCode("PRODUCT_CODE");
        request.setChannelCode("CHANNEL_CODE");

        List<PaidBillResponseWebDTO> microBillList = new ArrayList<>();
        List<PaidBillResponseWebDTO> harmoniBillList = new ArrayList<>();

        List<Payment> customerPaidBillList = new ArrayList<>();
        customerPaidBillList.add(new Payment());

        ChannelDTO requestChannel = new ChannelDTO();
        PaidBillResponseWebDTO paidBillResponseWebDTO = new PaidBillResponseWebDTO();
        InstitutionDTO institutionDTO = new InstitutionDTO();

        when(paymentRepository.findCustomerPaidBillList(any(LocalDate.class), eq("12345"), eq("PAID"), eq("PRODUCT_CODE")))
            .thenReturn(customerPaidBillList);
        when(channelService.findChannelByChannelCode(anyString())).thenReturn(requestChannel);
        when(channelService.areChannelsTheSameAccountingGroup(any(ChannelDTO.class), any(ChannelDTO.class)))
            .thenReturn(true);
        when(paymentMapper.toDTO(any(Payment.class))).thenReturn(paidBillResponseWebDTO);
        when(paymentMapper.toPaidBillResponseWebDTO(any(PaidBillResponseWebDTO.class), any(InstitutionDTO.class)))
            .thenReturn(paidBillResponseWebDTO);
        when(institutionService.getInstitutionById(anyLong())).thenReturn(institutionDTO);
        when(billPaymentRestFacade.getCustomerPaidBillList("12345")).thenReturn(new ResponseGetCustomerPaidBillList());

        // When
        GetCustomerPaidBillListResponse response = paymentServiceImpl.getCustomerPaidBillList(request);

        // Then
        assertNotNull(response);
        assertTrue(response.getBillList().isEmpty());
    }

@Test
public void testParseSubscriberNoIntoParts() {
    // Given
    ParseSubscriberNoIntoPartsRequest request = new ParseSubscriberNoIntoPartsRequest();
    request.setDebtTypeID(null);
    request.setProductCode("PRODUCT_CODE");
    request.setInstitutionCode("INSTITUTION_CODE");
    request.setSubscriberNo("SUBSCRIBER_NO");

    List<InstitutionUserIntfDTO> institutionUserIntfDTOList = new ArrayList<>();
    List<SubsrciberNoPartResponseWebDTO> subscriberNoIntoPartList = new ArrayList<>();

    when(institutionUserIntService.getDefaultUserInterface(anyString(), anyString())).thenReturn(institutionUserIntfDTOList);
    when(SubscriberNumberUtils.parseSubscriberNoIntoParts(anyList(), anyString())).thenReturn(subscriberNoIntoPartList);

    // When
    ParseSubscriberNoIntoPartsResponse response = paymentServiceImpl.parseSubscriberNoIntoParts(request);

    // Then
    assertNotNull(response);
    assertTrue(response.getSubsrciberNoPartResponseWebDTO().isEmpty());
}
@Test
public void testGetMicroBillList() throws MicroException {
    // Given
    GetCustomerPaidBillListRequest request = new GetCustomerPaidBillListRequest();
    request.setCustomerNo("12345");
    request.setProductCode("PRODUCT_CODE");

    List<PaidBillResponseWebDTO> paidBillResponseWebDTOList = new ArrayList<>();

    when(paymentRepository.findCustomerPaidBillList(any(LocalDate.class), eq("12345"), eq("PAID"), eq("PRODUCT_CODE")))
        .thenReturn(new ArrayList<>());
    when(paymentMapper.toHmnPaidBillDTOList(anyList())).thenReturn(new ArrayList<>());

    // When
    List<HmnPaidBillDTO> response = paymentServiceImpl.getMicroBillList(request);

    // Then
    assertNotNull(response);
    assertTrue(response.isEmpty());
}
