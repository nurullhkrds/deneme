public class BillValidationUtilTest {

    @Test
    void testValidateConditionTrue() {
        assertDoesNotThrow(() -> 
            BillValidationUtil.validateCondition(true, mock(EnumBillResult.class), "AppName"));
    }

    @Test
    void testValidateConditionFalse() {
        EnumBillResult mockError = mock(EnumBillResult.class);
        doReturn("Error Explanation").when(mockError).getExplanation();

        BillException exception = assertThrows(BillException.class, () -> 
            BillValidationUtil.validateCondition(false, mockError, "AppName"));

        assertEquals("AppName", exception.getAppName());
        assertEquals(mockError, exception.getBillResult());
    }

    @Test
    void testValidateConditionWithArgsTrue() {
        assertDoesNotThrow(() -> 
            BillValidationUtil.validateConditionWithArgs("AppName", true, mock(EnumBillResult.class)));
    }

    @Test
    void testValidateConditionWithArgsFalseWithoutParameters() {
        EnumBillResult mockError = mock(EnumBillResult.class);
        doReturn(100L).when(mockError).getCode();
        doReturn("Error Explanation").when(mockError).getExplanation();
        doReturn(null).when(mockError).getParameterKey();

        BillException exception = assertThrows(BillException.class, () -> 
            BillValidationUtil.validateConditionWithArgs("AppName", false, mockError, "Arg1"));

        assertEquals("AppName", exception.getAppName());
        assertEquals(mockError, exception.getBillResult());
        assertEquals("Error Explanation", exception.getErrorMessage());
    }

    @Test
    void testValidateConditionWithArgsFalseWithParameters() {
        EnumBillResult mockError = mock(EnumBillResult.class);
        doReturn(100L).when(mockError).getCode();
        doReturn("Error Explanation with {0}").when(mockError).getExplanation();
        doReturn(List.of("Param1")).when(mockError).getParameterKey();

        BillException exception = assertThrows(BillException.class, () -> 
            BillValidationUtil.validateConditionWithArgs("AppName", false, mockError, "Arg1"));

        assertEquals("AppName", exception.getAppName());
        assertEquals(mockError, exception.getBillResult());
        assertEquals("Error Explanation with Arg1", exception.getErrorMessage());
        assertEquals("Arg1", exception.getParameters().get("Param1"));
    }

    @Test
    void testThrowBillExceptionWithBillResult() {
        EnumBillResult mockError = mock(EnumBillResult.class);
        
        BillException exception = assertThrows(BillException.class, () -> 
            BillValidationUtil.throwBillException("AppName", mockError));

        assertEquals("AppName", exception.getAppName());
        assertEquals(mockError, exception.getBillResult());
    }

    @Test
    void testThrowBillExceptionWithException() {
        Exception mockException = new Exception("Test Exception");
        
        BillException exception = assertThrows(BillException.class, () -> 
            BillValidationUtil.throwBillException("AppName", mockException));

        assertEquals("AppName", exception.getAppName());
        assertEquals(mockException, exception.getCause());
    }
}
