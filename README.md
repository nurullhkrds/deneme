  @Test
    public void testHarmoniToMicroChannel() {
        String harmoniChannel = "WEB";
        String expectedMicroChannel = "WEB_MICRO";

        try (MockedStatic<ChannelUtil> mockedChannelUtil = mockStatic(ChannelUtil.class)) {
            mockedChannelUtil.when(() -> ChannelUtil.convertChannel(harmoniChannel)).thenReturn(expectedMicroChannel);

            String result = mapper.harmoniToMicroChannel(harmoniChannel);

            assertEquals(expectedMicroChannel, result);
        }
    }
