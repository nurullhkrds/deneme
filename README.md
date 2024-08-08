  @Test
    public void testGetCustomerPaidBillList() throws MicroException {
        // Given
        GetCustomerPaidBillListRequest request = new GetCustomerPaidBillListRequest();
        request.setCustomerNo("12345");
        request.setProductCode("PRODUCT_CODE");
        request.setChannelCode("CHANNEL_CODE");

        List<PaidBillResponseWebDTO> microBillList = new ArrayList<>();
        List<PaidBillResponseWebDTO> harmoniBillList = new ArrayList<>();

        when(paymentRepository.findCustomerPaidBillList(any(LocalDate.class), eq("12345"), eq("PAID"), eq("PRODUCT_CODE")))
            .thenReturn(new ArrayList<>());
        when(channelService.findChannelByChannelCode(anyString())).thenReturn(new ChannelDTO());
        when(channelService.areChannelsTheSameAccountingGroup(any(ChannelDTO.class), any(ChannelDTO.class)))
            .thenReturn(true);
        when(paymentMapper.toDTO(any(Payment.class))).thenReturn(new PaidBillResponseWebDTO());
        when(paymentMapper.toPaidBillResponseWebDTO(any(PaidBillResponseWebDTO.class), any(InstitutionDTO.class)))
            .thenReturn(new PaidBillResponseWebDTO());
        when(billPaymentRestFacade.getCustomerPaidBillList("12345")).thenReturn(new ResponseGetCustomerPaidBillList());

        // When
        GetCustomerPaidBillListResponse response = paymentServiceImpl.getCustomerPaidBillList(request);

        // Then
        assertNotNull(response);
        assertTrue(response.getBillList().isEmpty());
    }
