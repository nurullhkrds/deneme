@Test
void updateInstitutionChannel_shouldThrowDataConflictException_whenDuplicateInstitutionChannelExists() {
    UpdateInstitutionChannelRequestDTO requestDTO = new UpdateInstitutionChannelRequestDTO();
    requestDTO.setId(1L);
    requestDTO.setInstitutionDebtTypeId(2L);
    requestDTO.setChannelCode("CHANNEL_CODE");
    
    InstitutionChannel existingInstitutionChannel = new InstitutionChannel();
    existingInstitutionChannel.setId(2L);

    // Make sure this returns a non-empty value to avoid DataNotFoundException
    InstitutionChannel existingChannelToUpdate = new InstitutionChannel();
    existingChannelToUpdate.setId(1L);
    when(institutionChannelRepository.findById(requestDTO.getId())).thenReturn(Optional.of(existingChannelToUpdate));
    
    // Simulate a duplicate entity being present for the provided debt type and channel code
    when(institutionChannelRepository.findByInstitutionDebtTypeIdAndChannelCode(requestDTO.getInstitutionDebtTypeId(), requestDTO.getChannelCode()))
            .thenReturn(Optional.of(existingInstitutionChannel));

    // Verify that DataConflictException is thrown
    assertThrows(DataConflictException.class, () -> adminInstitutionChannelService.updateInstitutionChannel(requestDTO));

    verify(institutionChannelRepository, times(1)).findById(requestDTO.getId());
    verify(institutionChannelRepository, times(1)).findByInstitutionDebtTypeIdAndChannelCode(requestDTO.getInstitutionDebtTypeId(), requestDTO.getChannelCode());
}
