 @Test
    public void testToNotifyPaymentResponse() {
        NotifyPaymentProcessOutput result = new NotifyPaymentProcessOutput();
        result.setResult(EnumBillResult.SUCCESS);
        NotifyPaymentResponse response = mapper.toNotifyPaymentResponse(result);

        assertNotNull(response);
        assertEquals("SUCCESS", response.getReturnCode());
    }


org.opentest4j.AssertionFailedError: 
Expected :SUCCESS
Actual   :0
