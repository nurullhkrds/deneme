@Test
void testGetProcessChannel_WithValidInputs() {
    // Arrange
    ProcessChannelDTO processChannelDTO = new ProcessChannelDTO();
    when(processChannelService.findProcessChannel(anyString(), anyString())).thenReturn(processChannelDTO);

    // Act
    ProcessChannelDTO result = processService.getProcessChannel("code", "channelCode");

    // Assert
    assertNotNull(result);
    assertEquals(processChannelDTO, result);
    verify(processChannelService, times(1)).findProcessChannel("code", "channelCode");
}

@Test
void testGetProcessChannel_WithInvalidInputs_EmptyCode() {
    // Act
    ProcessChannelDTO result = processService.getProcessChannel("", "channelCode");

    // Assert
    assertNull(result);
    verify(processChannelService, times(0)).findProcessChannel(anyString(), anyString());
}

@Test
void testGetProcessChannel_WithInvalidInputs_EmptyChannelCode() {
    // Act
    ProcessChannelDTO result = processService.getProcessChannel("code", "");

    // Assert
    assertNull(result);
    verify(processChannelService, times(0)).findProcessChannel(anyString(), anyString());
}

@Test
void testGetProcessChannel_WithInvalidInputs_EmptyCodeAndChannelCode() {
    // Act
    ProcessChannelDTO result = processService.getProcessChannel("", "");

    // Assert
    assertNull(result);
    verify(processChannelService, times(0)).findProcessChannel(anyString(), anyString());
}
