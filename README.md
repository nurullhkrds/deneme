 @Test
    public void testGetCustomerPaidBillList() throws MicroException {
        // Arrange
        GetCustomerPaidBillListRequest request = new GetCustomerPaidBillListRequest();
        request.setCustomerNo("123");
        request.setChannelCode("channelCode");
        request.setProductCode("productCode");

        List<Payment> customerPaidBillList = new ArrayList<>();
        // Mock Payment nesneleri oluşturup listeye ekleyin
        when(paymentRepository.findCustomerPaidBillList(any(LocalDate.class), anyString(), anyString(), anyString()))
                .thenReturn(customerPaidBillList);

        ChannelDTO requestChannel = new ChannelDTO();
        // Mock ChannelDTO nesnesi oluşturun ve set edin
        when(channelService.findChannelByChannelCode(anyString())).thenReturn(requestChannel);
        when(channelService.areChannelsTheSameAccountingGroup(any(ChannelDTO.class), any(ChannelDTO.class)))
                .thenReturn(true);

        PaidBillResponseWebDTO paidBillResponseWebDTO = new PaidBillResponseWebDTO();
        // Mock PaidBillResponseWebDTO nesnesi oluşturun
        when(paymentMapper.toDTO(any(Payment.class))).thenReturn(paidBillResponseWebDTO);
        when(paymentMapper.toPaidBillResponseWebDTO(any(Payment.class), any(Institution.class)))
                .thenReturn(paidBillResponseWebDTO);

        Institution institution = new Institution();
        when(institutionService.getInstitutionById(anyString())).thenReturn(institution);

        // Act
        GetCustomerPaidBillListResponse response = billService.getCustomerPaidBillList(request);

        // Assert
        assertEquals(0, response.getBillList().size());
    }
