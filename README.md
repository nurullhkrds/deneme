  @Test
    void testValidateConditionWithArgsFalseWithParameters() {
        // EnumBillResult sabitini doğrudan kullanıyoruz
        EnumBillResult error = EnumBillResult.SOME_ERROR_CONSTANT_WITH_PARAMS;

        // `validateConditionWithArgs` metodunu test ediyoruz
        BillException exception = assertThrows(BillException.class, () -> 
            BillValidationUtil.validateConditionWithArgs("AppName", false, error, "Arg1", "Arg2"));

        // Sonuçları kontrol ediyoruz
        assertEquals("AppName", exception.getAppName());
        assertEquals(error, exception.getBillResult());

        // Beklenen hata mesajı ve parametreleri kontrol ediyoruz
        String expectedErrorMessage = error.getExplanation().replace("{0}", "Arg1").replace("{1}", "Arg2");
        assertEquals(expectedErrorMessage, exception.getErrorMessage());

        // Parametrelerin doğru eşleştirildiğini kontrol ediyoruz
        Map<String, String> expectedParams = Map.of("Param1", "Arg1", "Param2", "Arg2");
        assertEquals(expectedParams, exception.getParameters());
    }
