 @Test
    void testAllExceptionDataFields() {
        // Testing the main exception data fields
        assertNotNull(BillExceptionsUI.SUCCESS.getMessage());
        assertNotNull(BillExceptionsUI.NOT_IMPLEMENTED.getMessage());
        assertNotNull(BillExceptionsUI.SERVICE_CALL_EXCEPTION.getMessage());
        assertNotNull(BillExceptionsUI.UNKNOWN_ERROR_OCCURRED.getMessage());

        // Testing the ValidationExceptions inner class fields
        assertNotNull(BillExceptionsUI.ValidationExceptions.SERVICE_DEFINITION_NOT_FOUND.getMessage());
        assertNotNull(BillExceptionsUI.ValidationExceptions.PARAMETER_RECORD_NOT_FOUND.getMessage());
        assertNotNull(BillExceptionsUI.ValidationExceptions.CONVERSION_FAILED.getMessage());
        assertNotNull(BillExceptionsUI.ValidationExceptions.DATA_NOT_FOUND.getMessage());
        assertNotNull(BillExceptionsUI.ValidationExceptions.RETURN_MAP_EXIST.getMessage());
        assertNotNull(BillExceptionsUI.ValidationExceptions.RETURN_MAP_DEFINITION_NOT_FOUND.getMessage());
        assertNotNull(BillExceptionsUI.ValidationExceptions.RETURN_MAP_AND_INSTITUTION_EXIST.getMessage());
        assertNotNull(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTION_PRODUCT.getMessage());
        assertNotNull(BillExceptionsUI.ValidationExceptions.INSTITUTION_NOT_FOUND.getMessage());
        assertNotNull(BillExceptionsUI.ValidationExceptions.OWNER_DEPARTMENT_NOT_FOUND.getMessage());
        assertNotNull(BillExceptionsUI.ValidationExceptions.PRODUCT_NOT_FOUND.getMessage());
        assertNotNull(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTION_FEATURE.getMessage());
        assertNotNull(BillExceptionsUI.ValidationExceptions.INSTITUTION_FEATURE_NOT_FOUND.getMessage());
        assertNotNull(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTION_DEBT_TYPE.getMessage());
        assertNotNull(BillExceptionsUI.ValidationExceptions.INSTITUTION_DEBT_TYPE_NOT_FOUND.getMessage());
        assertNotNull(BillExceptionsUI.ValidationExceptions.FEATURE_NOT_FOUND.getMessage());
    }
