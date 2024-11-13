@Test
void updateInstitutionChannel_shouldThrowDataConflictException_whenDuplicateInstitutionChannelExists() {
    UpdateInstitutionChannelRequestDTO requestDTO = new UpdateInstitutionChannelRequestDTO();
    requestDTO.setId(1L);
    requestDTO.setInstitutionDebtTypeId(2L);
    requestDTO.setChannelCode("CHANNEL_CODE");
    InstitutionChannel existingInstitutionChannel = new InstitutionChannel();
    existingInstitutionChannel.setId(2L);

    when(institutionChannelRepository.findById(requestDTO.getId())).thenReturn(Optional.of(new InstitutionChannel()));
    when(institutionChannelRepository.findByInstitutionDebtTypeIdAndChannelCode(requestDTO.getInstitutionDebtTypeId(), requestDTO.getChannelCode()))
            .thenReturn(Optional.of(existingInstitutionChannel));

    assertThrows(DataConflictException.class, () -> adminInstitutionChannelService.updateInstitutionChannel(requestDTO));

    verify(institutionChannelRepository, times(1)).findById(requestDTO.getId());
    verify(institutionChannelRepository, times(1)).findByInstitutionDebtTypeIdAndChannelCode(requestDTO.getInstitutionDebtTypeId(), requestDTO.getChannelCode());
}
