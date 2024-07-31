    @Test
    public void testHarmoniToMicroChannel() {
        String harmoniChannel = "WEB";
        String expectedMicroChannel = "WEB_MICRO";
        when(ChannelUtil.convertChannel(harmoniChannel)).thenReturn(expectedMicroChannel);

        String result = mapper.harmoniToMicroChannel(harmoniChannel);

        assertEquals(expectedMicroChannel, result);
    }

Also, this error might show up because:
1. you stub either of: final/private/equals()/hashCode() methods.
   Those methods *cannot* be stubbed/verified.
   Mocking methods declared on non-public parent classes is not supported.
2. inside when() you don't call method on mock but on some other object.
