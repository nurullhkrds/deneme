    @Override
    public GetNotPaidBillCountWithOrderListResponse getNotPaidBillCountWithOrderList(GetNotPaidBillCountWithOrderListRequest request) {
        GetNotPaidBillCountWithOrderListResponse response = new GetNotPaidBillCountWithOrderListResponse();
        response = paymentOrderDataService.getNotPaidBillCountWithOrderList(request);

        if (CollectionUtils.isEmpty(response.getNotPaidBillCountWithOrderList())) {
            return null;

        }


        ResponseGetOrderedBillsDetailWithClientNo orderedBillsDetail = client.getOrderedBillsDetailWithClientNo(microToHarmoniMapper.toRequestGetOrderedBillsDetailWithClientNo(request));

        Integer microNotPaidCount = response.getNotPaidBillCountWithOrderList().get(0).getCountOfNotPaidBills();

        if (!CollectionUtils.isEmpty(orderedBillsDetail.getOrderedBills())){
            microNotPaidCount += orderedBillsDetail.getOrderedBills().get(0).getCountOfNotPaidBillsOrder();

        }

        for (NotPaidBillCountWithOrderWebDTO list : response.getNotPaidBillCountWithOrderList()) {
            list.setCountOfNotPaidBills(microNotPaidCount);
        }


        response.setHasNotPaidOrderBills(orderedBillsDetail.isHasNotPaidOrderBills());
        response.setTotalCount(Long.valueOf(orderedBillsDetail.getNamedQueryRowCount()));
        response.setNotPaidBillCountWithOrderList(harmoniToMicroMapper.toNotPaidBillCountWithOrderWebDTOList(orderedBillsDetail.getOrderedBills()));


        return response;
    }


  @Test
    void getNotPaidBillCountWithOrderList_mapsAndReturns_whenOrderListIsNotNull() {
        GetNotPaidBillCountWithOrderListRequest request = new GetNotPaidBillCountWithOrderListRequest();
        RequestGetOrderedBillsDetailWithClientNo externalReq = new RequestGetOrderedBillsDetailWithClientNo();
        when(microtoHarmoniMapper.toRequestGetOrderedBillsDetailWithClientNo(request)).thenReturn(externalReq);

        ResponseGetOrderedBillsDetailWithClientNo externalResp = new ResponseGetOrderedBillsDetailWithClientNo();
        List<OrderedBillDetailDTO> extOrder = List.of(new OrderedBillDetailDTO());
        externalResp.setOrderedBills(extOrder);
        externalResp.setNamedQueryRowCount(1);

        GetNotPaidBillCountWithOrderListRequest requestNotPaid = new GetNotPaidBillCountWithOrderListRequest();
        GetNotPaidBillCountWithOrderListResponse responseNotPaid = new GetNotPaidBillCountWithOrderListResponse();

        List<NotPaidBillCountWithOrderWebDTO> notPaidBillCountWithOrderList = List.of(new NotPaidBillCountWithOrderWebDTO());
        responseNotPaid.setNotPaidBillCountWithOrderList(notPaidBillCountWithOrderList);
        responseNotPaid.setTotalCount(1L);
        when(paymentOrderDataService.getNotPaidBillCountWithOrderList(requestNotPaid)).thenReturn(responseNotPaid);


        when(client.getOrderedBillsDetailWithClientNo(externalReq)).thenReturn(externalResp);

        List<NotPaidBillCountWithOrderWebDTO> mapped = List.of(new NotPaidBillCountWithOrderWebDTO());
        when(harmoniToMicroMapper.toNotPaidBillCountWithOrderWebDTOList(extOrder)).thenReturn(mapped);

        GetNotPaidBillCountWithOrderListResponse response = service.getNotPaidBillCountWithOrderList(request);

        assertNotNull(response);

        assertEquals(mapped, response.getNotPaidBillCountWithOrderList());
        verify(microtoHarmoniMapper).toRequestGetOrderedBillsDetailWithClientNo(request);
        verify(client).getOrderedBillsDetailWithClientNo(externalReq);
        verify(paymentOrderDataService).getNotPaidBillCountWithOrderList(requestNotPaid);
        verify(harmoniToMicroMapper).toNotPaidBillCountWithOrderWebDTOList(extOrder);
    }


java.lang.NullPointerException: Cannot invoke ".response.GetNotPaidBillCountWithOrderListResponse.getNotPaidBillCountWithOrderList()" because "response" is null
