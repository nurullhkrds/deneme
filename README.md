@Test
public void testGetCustomerPaidBillList() throws MicroException {
    // Arrange
    GetCustomerPaidBillListRequest request = new GetCustomerPaidBillListRequest();
    request.setCustomerNo("123");
    request.setChannelCode("channelCode");
    request.setProductCode("productCode");

    // Mock customerPaidBillList with one Payment object
    List<Payment> customerPaidBillList = new ArrayList<>();
    Payment payment = new Payment();
    customerPaidBillList.add(payment);

    // Mock hmnPaidBillList with one HmnPaidBillDTO object
    List<HmnPaidBillDTO> hmnPaidBillList = new ArrayList<>();
    HmnPaidBillDTO hmnPaidBillDTO = new HmnPaidBillDTO();
    hmnPaidBillList.add(hmnPaidBillDTO);

    PaidBillResponseWebDTO paidBillResponseWebDTO = new PaidBillResponseWebDTO();

    when(paymentRepository.findCustomerPaidBillList(any(LocalDate.class), anyString(), anyString(), anyString()))
            .thenReturn(customerPaidBillList);

    ChannelDTO requestChannel = new ChannelDTO();
    when(channelService.findChannelByChannelCode(anyString())).thenReturn(requestChannel);
    when(channelService.areChannelsTheSameAccountingGroup(any(ChannelDTO.class), any(ChannelDTO.class)))
            .thenReturn(true);

    when(paymentMapper.toDTO(any(Payment.class))).thenReturn(paidBillResponseWebDTO);
    when(paymentMapper.toPaidBillResponseWebDTO(any(Payment.class), any(Institution.class)))
            .thenReturn(paidBillResponseWebDTO);

    Institution institution = new Institution();
    when(institutionService.getInstitutionById(anyString())).thenReturn(institution);

    ResponseGetCustomerPaidBillList harmoniResponse = new ResponseGetCustomerPaidBillList();
    harmoniResponse.setStatus("SUCCESS");
    harmoniResponse.setBillDTOList(hmnPaidBillList);

    when(billPaymentRestFacade.getCustomerPaidBillList(anyString())).thenReturn(harmoniResponse);
    when(paymentMapper.toPaidBillResponseWebDTO(any(HmnPaidBillDTO.class))).thenReturn(paidBillResponseWebDTO);

    // Act
    GetCustomerPaidBillListResponse response = billService.getCustomerPaidBillList(request);

    // Assert
    assertNotNull(response.getBillList());
    assertEquals(2, response.getBillList().size()); // Verifying that the list contains items from both sources
}
