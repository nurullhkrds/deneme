
    @Test
    void testGetBillList() {
        // Test verilerinin hazırlanması
        GetCustomerPaidBillListRequest request = new GetCustomerPaidBillListRequest();
        request.setCustomerNo("12345");
        request.setChannelCode("channelCode");
        request.setProductCode("productCode");

        Payment payment = new Payment();
        payment.setChannelCode("paymentChannelCode");
        payment.setInstitutionId("institutionId");

        List<Payment> paymentList = new ArrayList<>();
        paymentList.add(payment);

        ChannelDTO requestChannel = new ChannelDTO();
        ChannelDTO paymentChannel = new ChannelDTO();

        PaidBillResponseWebDTO dto = new PaidBillResponseWebDTO();
        Institution institution = new Institution();

        // Mock davranışlarının ayarlanması
        when(paymentRepository.findCustomerPaidBillList(any(LocalDate.class), eq("12345"), eq("PAID"), eq("productCode")))
            .thenReturn(paymentList);

        when(channelService.findChannelByChannelCode(eq("channelCode")))
            .thenReturn(requestChannel);

        when(channelService.findChannelByChannelCode(eq("paymentChannelCode")))
            .thenReturn(paymentChannel);

        when(channelService.areChannelsTheSameAccountingGroup(eq(requestChannel), eq(paymentChannel)))
            .thenReturn(true);

        when(paymentMapper.toDTO(any(Payment.class)))
            .thenReturn(dto);

        when(paymentMapper.toPaidBillResponseWebDTO(any(Payment.class), any(Institution.class)))
            .thenReturn(dto);

        when(institutionService.getInstitutionById(eq("institutionId")))
            .thenReturn(institution);

        // Metodun çağrılması
        List<PaidBillResponseWebDTO> result = paymentService.getBillList(request);

        // Sonuçların doğrulanması
        assertNotNull(result);
        assertEquals(1, result.size());
        verify(paymentRepository, times(1)).findCustomerPaidBillList(any(LocalDate.class), eq("12345"), eq("PAID"), eq("productCode"));
        verify(channelService, times(1)).findChannelByChannelCode(eq("channelCode"));
        verify(channelService, times(1)).findChannelByChannelCode(eq("paymentChannelCode"));
        verify(channelService, times(1)).areChannelsTheSameAccountingGroup(eq(requestChannel), eq(paymentChannel));
        verify(paymentMapper, times(1)).toDTO(any(Payment.class));
        verify(paymentMapper, times(1)).toPaidBillResponseWebDTO(any(Payment.class), any(Institution.class));
        verify(institutionService, times(1)).getInstitutionById(eq("institutionId"));
    }
