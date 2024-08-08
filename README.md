@Test
    public void testGetCustomerPaidBillList() throws MicroException {
        // Arrange
        GetCustomerPaidBillListRequest request = new GetCustomerPaidBillListRequest();
        request.setCustomerNo("123");
        request.setChannelCode("channelCode");
        request.setProductCode("productCode");

        List<Payment> customerPaidBillList = new ArrayList<>();
        List<HmnPaidBillDTO> hmnPaidBillList = new ArrayList<>();
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
    }

    @Test
    public void testGetCustomerPaidBillList_NoBillsFound() throws MicroException {
        // Arrange
        GetCustomerPaidBillListRequest request = new GetCustomerPaidBillListRequest();
        request.setCustomerNo("123");
        request.setChannelCode("channelCode");
        request.setProductCode("productCode");

        when(paymentRepository.findCustomerPaidBillList(any(LocalDate.class), anyString(), anyString(), anyString()))
                .thenReturn(new ArrayList<>());

        ChannelDTO requestChannel = new ChannelDTO();
        when(channelService.findChannelByChannelCode(anyString())).thenReturn(requestChannel);

        ResponseGetCustomerPaidBillList harmoniResponse = new ResponseGetCustomerPaidBillList();
        harmoniResponse.setStatus("SUCCESS");
        harmoniResponse.setBillDTOList(new ArrayList<>());

        when(billPaymentRestFacade.getCustomerPaidBillList(anyString())).thenReturn(harmoniResponse);

        // Act & Assert
        MicroException exception = assertThrows(MicroException.class, () -> {
            billService.getCustomerPaidBillList(request);
        });

        assertEquals(EnumBillResult.PAID_BILL_NOT_FOUND_ERROR.getMessage(), exception.getMessage());
    }
