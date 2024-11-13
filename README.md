  @Test
    void updateInstitutionChannel_shouldThrowDataNotFoundException_whenInstitutionChannelDoesNotExist() {
        UpdateInstitutionChannelRequestDTO requestDTO = new UpdateInstitutionChannelRequestDTO();
        requestDTO.setId(1L);

        when(institutionChannelRepository.findById(requestDTO.getId())).thenReturn(Optional.empty());

        assertThrows(DataNotFoundException.class, () -> adminInstitutionChannelService.updateInstitutionChannel(requestDTO));
        verify(institutionChannelRepository, times(1)).findById(requestDTO.getId());
    }

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

    @Test
    void updateInstitutionChannel_shouldReturnUpdatedInstitutionChannelDTO_whenSuccessful() throws MicroException {
        UpdateInstitutionChannelRequestDTO requestDTO = new UpdateInstitutionChannelRequestDTO();
        requestDTO.setId(1L);
        requestDTO.setInstitutionDebtTypeId(2L);
        requestDTO.setChannelCode("CHANNEL_CODE");
        requestDTO.setIsNewBillNeeded(true);
        requestDTO.setIsOverPaymentAllowed(true);
        requestDTO.setIsPartialPaymentAllowed(true);
        requestDTO.setWorkingStartTime("09:00");
        requestDTO.setWorkingFinishTime("18:00");
        requestDTO.setIsActive(true);

        InstitutionChannel existingInstitutionChannel = new InstitutionChannel();
        InstitutionChannelDTO existingInstitutionChannelDTO = new InstitutionChannelDTO();
        InstitutionDebtTypeDTO institutionDebtTypeDTO = new InstitutionDebtTypeDTO();
        ChannelDTO channelDTO = new ChannelDTO();
        InstitutionChannel updatedInstitutionChannel = new InstitutionChannel();
        InstitutionChannelDTO updatedInstitutionChannelDTO = new InstitutionChannelDTO();

        when(institutionChannelRepository.findById(requestDTO.getId())).thenReturn(Optional.of(existingInstitutionChannel));
        when(institutionDebtTypeService.getInstitutionDebtTypeById(requestDTO.getInstitutionDebtTypeId())).thenReturn(institutionDebtTypeDTO);
        when(channelService.findChannelByChannelCode(requestDTO.getChannelCode())).thenReturn(channelDTO);
        when(institutionChannelMapper.toDTO(existingInstitutionChannel)).thenReturn(existingInstitutionChannelDTO);
        when(institutionChannelMapper.toEntity(existingInstitutionChannelDTO)).thenReturn(updatedInstitutionChannel);
        when(institutionChannelRepository.save(updatedInstitutionChannel)).thenReturn(updatedInstitutionChannel);
        when(institutionChannelMapper.toDTO(updatedInstitutionChannel)).thenReturn(updatedInstitutionChannelDTO);

        InstitutionChannelDTO result = adminInstitutionChannelService.updateInstitutionChannel(requestDTO);

        assertNotNull(result);
        assertEquals(updatedInstitutionChannelDTO, result);
        verify(institutionChannelRepository, times(1)).findById(requestDTO.getId());
        verify(institutionDebtTypeService, times(1)).getInstitutionDebtTypeById(requestDTO.getInstitutionDebtTypeId());
        verify(channelService, times(1)).findChannelByChannelCode(requestDTO.getChannelCode());
        verify(institutionChannelMapper, times(1)).toDTO(existingInstitutionChannel);
        verify(institutionChannelMapper, times(1)).toEntity(existingInstitutionChannelDTO);
        verify(institutionChannelRepository, times(1)).save(updatedInstitutionChannel);
        verify(institutionChannelMapper, times(1)).toDTO(updatedInstitutionChannel);
    }
