@Test
    void testLpadWithZeros_NullInput() {
        // Null input
        String result = lpadWithZeros(null, 5);
        assertNull(result, "Expected null for null input");
    }

    @Test
    void testLpadWithZeros_InputLongerThanTotalLength() {
        // Input longer than total length
        String result = lpadWithZeros("123456", 5);
        assertEquals("12345", result, "Expected truncated input for input longer than total length");
    }

    @Test
    void testLpadWithZeros_InputShorterThanTotalLength() {
        // Input shorter than total length
        String result = lpadWithZeros("123", 5);
        assertEquals("00123", result, "Expected padded input for input shorter than total length");
    }

    @Test
    void testLpadWithZeros_InputEqualToTotalLength() {
        // Input equal to total length
        String result = lpadWithZeros("12345", 5);
        assertEquals("12345", result, "Expected same input for input equal to total length");
    }

    @Test
    void testLpadWithZeros_InputContainsNonNumericCharacters() {
        // Non-numeric input (should throw an exception)
        assertThrows(NumberFormatException.class, () -> lpadWithZeros("12A", 5), "Expected NumberFormatException for non-numeric input");
    }

    @Test
    void testLpadWithZeros_InputIsZero() {
        // Input is "0"
        String result = lpadWithZeros("0", 5);
        assertEquals("00000", result, "Expected padded zero input");
    }

    @Test
    void testLpadWithZeros_TotalLengthZero() {
        // Total length is zero
        String result = lpadWithZeros("123", 0);
        assertEquals("", result, "Expected empty string for total length of zero");
    }
