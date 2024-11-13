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

        verify(institutionChannelRepository, times(1)).findById(requestDTO.getId());
        verify(institutionChannelRepository, times(1)).findByInstitutionDebtTypeIdAndChannelCode(requestDTO.getInstitutionDebtTypeId(), requestDTO.getChannelCode());
    }


Wanted but not invoked:
institutionChannelRepository.findById(1L);
-> at com.ykb.payments.bill.transaction.institution.admin.service.impl.AdminInstitutionChannelServiceImplTest.updateInstitutionChannel_shouldThrowDataConflictException_whenDuplicateInstitutionChannelExists(AdminInstitutionChannelServiceImplTest.java:182)
Actually, there were zero interactions with this mock.
