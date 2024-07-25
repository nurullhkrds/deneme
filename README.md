@Test
    void getCustomerPaidBillList_shouldReturnCombinedBillList_whenBothListsAreNotEmpty() throws MicroException {
        // Given
        GetCustomerPaidBillListRequest request = new GetCustomerPaidBillListRequest();
        List<PaidBillResponseWebDTO> microBillList = new ArrayList<>();
        List<PaidBillResponseWebDTO> harmoniBillList = new ArrayList<>();
        PaidBillResponseWebDTO microBill = new PaidBillResponseWebDTO();
        PaidBillResponseWebDTO harmoniBill = new PaidBillResponseWebDTO();
        microBillList.add(microBill);
        harmoniBillList.add(harmoniBill);
        GetCustomerPaidBillListResponse expectedResponse = new GetCustomerPaidBillListResponse();
        expectedResponse.setBillList(List.of(microBill, harmoniBill));

        ResponseGetCustomerPaidBillList harmoniResponse = new ResponseGetCustomerPaidBillList();
        harmoniResponse.setStatus(SUCCESS);
        harmoniResponse.setBillDTOList(Collections.singletonList(new HmnPaidBillDTO()));

        when(billPaymentRestFacade.getCustomerPaidBillList(request.getCustomerNo())).thenReturn(harmoniResponse);
        when(paymentRepository.findCustomerPaidBillList(any(), any(), any(), any())).thenReturn(microBillList);
        when(paymentMapper.toPaidBillResponseWebDTO(any())).thenReturn(microBill);
        when(paymentMapper.toDTO(any())).thenReturn(new Payment());
        when(channelService.findChannelByChannelCode(any())).thenReturn(new ChannelDTO());
        when(channelService.areChannelsTheSameAccountingGroup(any(), any())).thenReturn(true);
        when(institutionService.getInstitutionById(any())).thenReturn(new InstitutionDTO());

        // When
        GetCustomerPaidBillListResponse actualResponse = paymentService.getCustomerPaidBillList(request);

        // Then
        assertEquals(expectedResponse, actualResponse);
    }

    @Test
    void getCustomerPaidBillList_shouldReturnEmptyList_whenBothListsAreEmpty() throws MicroException {
        // Given
        GetCustomerPaidBillListRequest request = new GetCustomerPaidBillListRequest();
        List<PaidBillResponseWebDTO> emptyList = Collections.emptyList();
        GetCustomerPaidBillListResponse expectedResponse = new GetCustomerPaidBillListResponse();
        expectedResponse.setBillList(Collections.emptyList());

        ResponseGetCustomerPaidBillList harmoniResponse = new ResponseGetCustomerPaidBillList();
        harmoniResponse.setStatus(SUCCESS);
        harmoniResponse.setBillDTOList(Collections.emptyList());

        when(billPaymentRestFacade.getCustomerPaidBillList(request.getCustomerNo())).thenReturn(harmoniResponse);
        when(paymentRepository.findCustomerPaidBillList(any(), any(), any(), any())).thenReturn(Collections.emptyList());
        when(paymentMapper.toPaidBillResponseWebDTO(any())).thenReturn(new PaidBillResponseWebDTO());
        when(paymentMapper.toDTO(any())).thenReturn(new Payment());
        when(channelService.findChannelByChannelCode(any())).thenReturn(new ChannelDTO());
        when(channelService.areChannelsTheSameAccountingGroup(any(), any())).thenReturn(true);
        when(institutionService.getInstitutionById(any())).thenReturn(new InstitutionDTO());

        // When
        GetCustomerPaidBillListResponse actualResponse = paymentService.getCustomerPaidBillList(request);

        // Then
        assertEquals(expectedResponse, actualResponse);
    }
