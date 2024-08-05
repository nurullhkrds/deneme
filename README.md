 @Test
    void testGetCustomerPaidBillList_Success() throws MicroException {
        // Arrange
        Long customerNo = 123L;
        String channelCode = "someChannel";
        String product = "someProduct";
        GetCustomerPaidBillListRequest request = new GetCustomerPaidBillListRequest();
        request.setCustomerNo(customerNo);
        request.setChannelCode(ChannelUtil.convertChannel(channelCode));
        request.setProductCode(product);

        List<HmnPaidBillDTO> hmnPaidBillDTOList = Collections.singletonList(new HmnPaidBillDTO());
        when(paymentService.getMicroBillList(any())).thenReturn(hmnPaidBillDTOList);

        // Act
        HarmoniCoreServiceResultDTO<ResponseHarmoniGetCustomerPaidBillList> result = harmoniPaymentAdkController.getCustomerPaidBillList(customerNo, channelCode, product);

        // Assert
        assertNotNull(result);
        assertEquals("S", result.getStatus());
        assertNotNull(result.getResult());
        assertEquals(hmnPaidBillDTOList, result.getResult().getBillDTOList());
        assertEquals(EnumBillResult.SUCCESS.getHmnCode().get(0).getValue(), result.getResponseMessage().getResponseCode());
        assertEquals(EnumBillResult.SUCCESS.getHmnCode().get(0).getDescription(), result.getResponseMessage().getResponseMessage());
    }

    @Test
    void testGetCustomerPaidBillList_MicroException() throws MicroException {
        // Arrange
        Long customerNo = 123L;
        String channelCode = "someChannel";
        String product = "someProduct";
        MicroException microException = mock(MicroException.class);
        ExceptionData exceptionData = mock(ExceptionData.class);

        when(paymentService.getMicroBillList(any())).thenThrow(microException);
        when(microException.getExceptionData()).thenReturn(exceptionData);
        when(exceptionData.getErrorCode()).thenReturn(EnumBillResult.PAID_BILL_NOT_FOUND_ERROR.getHmnCode().get(0).getValue());
        when(exceptionData.getErrorMessage()).thenReturn(EnumBillResult.PAID_BILL_NOT_FOUND_ERROR.getHmnCode().get(0).getDescription());

        // Act
        HarmoniCoreServiceResultDTO<ResponseHarmoniGetCustomerPaidBillList> result = harmoniPaymentAdkController.getCustomerPaidBillList(customerNo, channelCode, product);

        // Assert
        assertNotNull(result);
        assertEquals("E", result.getStatus());
        assertNull(result.getResult());
        assertEquals(EnumBillResult.PAID_BILL_NOT_FOUND_ERROR.getHmnCode().get(0).getValue(), result.getResponseMessage().getResponseCode());
        assertEquals(EnumBillResult.PAID_BILL_NOT_FOUND_ERROR.getHmnCode().get(0).getDescription(), result.getResponseMessage().getResponseMessage());
    }
