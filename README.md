import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import com.ykb.architecture.micro.error.exception.DataConflictException;
import com.ykb.architecture.micro.error.exception.DataNotFoundException;
import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.common.exception.BillExceptionsUI;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionChannelMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminChannelService;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionDebtTypeService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionChannelRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionChannelRequestDTO;
import com.ykb.payments.bill.transaction.institution.domain.InstitutionChannel;
import com.ykb.payments.bill.transaction.institution.dto.ChannelDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionChannelDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionDebtTypeDTO;
import com.ykb.payments.bill.transaction.institution.enums.EnumFeatureCode;
import com.ykb.payments.bill.transaction.institution.repository.InstitutionChannelRepository;

import java.time.LocalDateTime;
import java.util.Optional;
import java.util.List;
import java.util.Arrays;

class AdminInstitutionChannelServiceImplTest {

    @Mock
    private InstitutionChannelRepository institutionChannelRepository;

    @Mock
    private AdminInstitutionChannelMapper institutionChannelMapper;

    @Mock
    private AdminInstitutionDebtTypeService institutionDebtTypeService;

    @Mock
    private AdminChannelService channelService;

    @InjectMocks
    private AdminInstitutionChannelServiceImpl adminInstitutionChannelService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getAllInstitutionChannels_shouldReturnListOfInstitutionChannelDTOs() {
        List<InstitutionChannel> institutionChannels = Arrays.asList(new InstitutionChannel(), new InstitutionChannel());
        List<InstitutionChannelDTO> institutionChannelDTOs = Arrays.asList(new InstitutionChannelDTO(), new InstitutionChannelDTO());

        when(institutionChannelRepository.findAll()).thenReturn(institutionChannels);
        when(institutionChannelMapper.toDTOList(institutionChannels)).thenReturn(institutionChannelDTOs);

        List<InstitutionChannelDTO> result = adminInstitutionChannelService.getAllInstitutionChannels();

        assertNotNull(result);
        assertEquals(2, result.size());
        verify(institutionChannelRepository, times(1)).findAll();
        verify(institutionChannelMapper, times(1)).toDTOList(institutionChannels);
    }

    @Test
    void getInstitutionChannelById_shouldReturnInstitutionChannelDTO_whenFound() {
        Long id = 1L;
        InstitutionChannel institutionChannel = new InstitutionChannel();
        InstitutionChannelDTO institutionChannelDTO = new InstitutionChannelDTO();

        when(institutionChannelRepository.findById(id)).thenReturn(Optional.of(institutionChannel));
        when(institutionChannelMapper.toDTO(institutionChannel)).thenReturn(institutionChannelDTO);

        InstitutionChannelDTO result = adminInstitutionChannelService.getInstitutionChannelById(id);

        assertNotNull(result);
        assertEquals(institutionChannelDTO, result);
        verify(institutionChannelRepository, times(1)).findById(id);
        verify(institutionChannelMapper, times(1)).toDTO(institutionChannel);
    }

    @Test
    void getInstitutionChannelById_shouldReturnNull_whenNotFound() {
        Long id = 1L;

        when(institutionChannelRepository.findById(id)).thenReturn(Optional.empty());

        InstitutionChannelDTO result = adminInstitutionChannelService.getInstitutionChannelById(id);

        assertNull(result);
        verify(institutionChannelRepository, times(1)).findById(id);
        verify(institutionChannelMapper, never()).toDTO(any());
    }

    @Test
    void createInstitutionChannel_shouldThrowDataConflictException_whenInstitutionChannelAlreadyExists() {
        CreateInstitutionChannelRequestDTO requestDTO = new CreateInstitutionChannelRequestDTO();
        requestDTO.setInstitutionDebtTypeId(1L);
        requestDTO.setChannelCode("CHANNEL_CODE");

        when(institutionChannelRepository.findByInstitutionDebtTypeIdAndChannelCode(requestDTO.getInstitutionDebtTypeId(), requestDTO.getChannelCode()))
                .thenReturn(Optional.of(new InstitutionChannel()));

        assertThrows(DataConflictException.class, () -> adminInstitutionChannelService.createInstitutionChannel(requestDTO));
        verify(institutionChannelRepository, times(1)).findByInstitutionDebtTypeIdAndChannelCode(requestDTO.getInstitutionDebtTypeId(), requestDTO.getChannelCode());
    }

    @Test
    void createInstitutionChannel_shouldThrowDataConflictException_whenInstitutionDebtTypeNotFound() throws MicroException {
        CreateInstitutionChannelRequestDTO requestDTO = new CreateInstitutionChannelRequestDTO();
        requestDTO.setInstitutionDebtTypeId(1L);
        requestDTO.setChannelCode("CHANNEL_CODE");

        when(institutionChannelRepository.findByInstitutionDebtTypeIdAndChannelCode(requestDTO.getInstitutionDebtTypeId(), requestDTO.getChannelCode()))
                .thenReturn(Optional.empty());
        when(institutionDebtTypeService.getInstitutionDebtTypeById(requestDTO.getInstitutionDebtTypeId())).thenReturn(null);

        assertThrows(DataConflictException.class, () -> adminInstitutionChannelService.createInstitutionChannel(requestDTO));
        verify(institutionChannelRepository, times(1)).findByInstitutionDebtTypeIdAndChannelCode(requestDTO.getInstitutionDebtTypeId(), requestDTO.getChannelCode());
        verify(institutionDebtTypeService, times(1)).getInstitutionDebtTypeById(requestDTO.getInstitutionDebtTypeId());
    }

    @Test
    void createInstitutionChannel_shouldReturnInstitutionChannelDTO_whenSuccessful() throws MicroException {
        CreateInstitutionChannelRequestDTO requestDTO = new CreateInstitutionChannelRequestDTO();
        requestDTO.setInstitutionDebtTypeId(1L);
        requestDTO.setChannelCode("CHANNEL_CODE");
        ChannelDTO channelDTO = new ChannelDTO();
        InstitutionDebtTypeDTO institutionDebtTypeDTO = new InstitutionDebtTypeDTO();
        InstitutionChannelDTO institutionChannelDTO = new InstitutionChannelDTO();
        InstitutionChannel institutionChannel = new InstitutionChannel();

        when(institutionChannelRepository.findByInstitutionDebtTypeIdAndChannelCode(requestDTO.getInstitutionDebtTypeId(), requestDTO.getChannelCode()))
                .thenReturn(Optional.empty());
        when(institutionDebtTypeService.getInstitutionDebtTypeById(requestDTO.getInstitutionDebtTypeId())).thenReturn(institutionDebtTypeDTO);
        when(channelService.findChannelByChannelCode(requestDTO.getChannelCode())).thenReturn(channelDTO);
        when(institutionChannelMapper.toDTO(requestDTO)).thenReturn(institutionChannelDTO);
        when(institutionChannelMapper.toEntity(institutionChannelDTO)).thenReturn(institutionChannel);
        when(institutionChannelRepository.save(institutionChannel)).thenReturn(institutionChannel);
        when(institutionChannelMapper.toDTO(institutionChannel)).thenReturn(institutionChannelDTO);

        InstitutionChannelDTO result = adminInstitutionChannelService.createInstitutionChannel(requestDTO);

        assertNotNull(result);
        assertEquals(institutionChannelDTO, result);
        verify(institutionChannelRepository, times(1)).findByInstitutionDebtTypeIdAndChannelCode(requestDTO.getInstitutionDebtTypeId(), requestDTO.getChannelCode());
        verify(institutionDebtTypeService, times(1)).getInstitutionDebtTypeById(requestDTO.getInstitutionDebtTypeId());
        verify(channelService, times(1)).findChannelByChannelCode(requestDTO.getChannelCode());
        verify(institutionChannelMapper, times(1)).toDTO(requestDTO);
        verify(institutionChannelMapper, times(1)).toEntity(institutionChannelDTO);
        verify(institutionChannelRepository, times(1)).save(institutionChannel);
        verify(institutionChannelMapper, times(1)).toDTO(institutionChannel);
    }
} 
