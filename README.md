public class BillValidationUtilTest {

    @Test
    void testValidateConditionTrue() {
        assertDoesNotThrow(() -> 
            BillValidationUtil.validateCondition(true, EnumBillResult.SOME_ERROR_CONSTANT, "AppName"));
    }

    @Test
    void testValidateConditionFalse() {
        EnumBillResult error = EnumBillResult.SOME_ERROR_CONSTANT;

        BillException exception = assertThrows(BillException.class, () -> 
            BillValidationUtil.validateCondition(false, error, "AppName"));

        assertEquals("AppName", exception.getAppName());
        assertEquals(error, exception.getBillResult());
    }

    @Test
    void testValidateConditionWithArgsTrue() {
        assertDoesNotThrow(() -> 
            BillValidationUtil.validateConditionWithArgs("AppName", true, EnumBillResult.SOME_ERROR_CONSTANT));
    }

    @Test
    void testValidateConditionWithArgsFalseWithoutParameters() {
        EnumBillResult error = EnumBillResult.SOME_ERROR_CONSTANT;

        BillException exception = assertThrows(BillException.class, () -> 
            BillValidationUtil.validateConditionWithArgs("AppName", false, error, "Arg1"));

        assertEquals("AppName", exception.getAppName());
        assertEquals(error, exception.getBillResult());
    }

    @Test
    void testValidateConditionWithArgsFalseWithParameters() {
        EnumBillResult error = EnumBillResult.SOME_ERROR_CONSTANT_WITH_PARAMS;

        BillException exception = assertThrows(BillException.class, () -> 
            BillValidationUtil.validateConditionWithArgs("AppName", false, error, "Arg1"));

        assertEquals("AppName", exception.getAppName());
        assertEquals(error, exception.getBillResult());
        assertEquals("Arg1", exception.getParameters().get("Param1"));  // Param1, error enumında tanımlı olmalı
    }

    @Test
    void testThrowBillExceptionWithBillResult() {
        EnumBillResult error = EnumBillResult.SOME_ERROR_CONSTANT;
        
        BillException exception = assertThrows(BillException.class, () -> 
            BillValidationUtil.throwBillException("AppName", error));

        assertEquals("AppName", exception.getAppName());
        assertEquals(error, exception.getBillResult());
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
