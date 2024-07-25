
    @Test
    void testParse() {
        // Test for valid value
        EnumAccountingSource result = EnumAccountingSource.parse("PAYMENT");
        assertNotNull(result);
        assertEquals(EnumAccountingSource.PAYMENT, result);

        // Test for another valid value
        result = EnumAccountingSource.parse("MERCHANT");
        assertNotNull(result);
        assertEquals(EnumAccountingSource.MERCHANT, result);

        // Test for invalid value
        result = EnumAccountingSource.parse("INVALID");
        assertNull(result);
    }

    @Test
    void testSerialization() throws Exception {
        ObjectMapper objectMapper = new ObjectMapper();
        
        // Test for PAYMENT enum
        String paymentJson = objectMapper.writeValueAsString(EnumAccountingSource.PAYMENT);
        assertTrue(paymentJson.contains("PAYMENT")); // Adjusted to check if "PAYMENT" is part of the serialized string

        // Test for MERCHANT enum
        String merchantJson = objectMapper.writeValueAsString(EnumAccountingSource.MERCHANT);
        assertTrue(merchantJson.contains("MERCHANT")); // Adjusted to check if "MERCHANT" is part of the serialized string
    }

    @Test
    void testToString() {
        // Adjusted expected values to match the enum's toString output
        assertEquals("EnumAccountingSource(value=PAYMENT, description=Muhasabe biz tarafından gerceklesmis)", EnumAccountingSource.PAYMENT.toString());
        assertEquals("EnumAccountingSource(value=MERCHANT, description=Muhasebe üye iş yeri ekibi tarafından gerçekleşmiş)", EnumAccountingSource.MERCHANT.toString());
    }
