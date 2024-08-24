@Test
void shouldFormatSubscriberNumberParts() {
    // Arrange
    InstitutionUserIntfDTO institutionUserIntfDto = getInstitutionUserIntfDto();
    SubscriberNoPartRequestDTO subscriberNoPartRequestDTO = getSubscriberNoPartRequestDTO();

    institutionUserIntfDto.setCompleteLengthFlag(true);
    institutionUserIntfDto.setMaxLength(5);

    List<InstitutionUserIntfDTO> institutionUserIntfDTOList = List.of(institutionUserIntfDto);
    List<SubscriberNoPartRequestDTO> subscriberNoPartRequestDTOList = List.of(subscriberNoPartRequestDTO);

    // Act
    String formattedSubscriberNumber = SubscriberNumberUtils.formatSubscriberNumberParts(institutionUserIntfDTOList, subscriberNoPartRequestDTOList);

    // Assert
    Assertions.assertEquals("00123", subscriberNoPartRequestDTOList.get(0).getPartValue(), "Part value should be left-padded to match maxLength");
    Assertions.assertEquals("00123", formattedSubscriberNumber, "Formatted subscriber number should match expected value");
}
