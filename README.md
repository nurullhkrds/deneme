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
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionChannelProcessMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionChannelService;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionProcessService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionChannelProcessRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionChannelProcessRequestDTO;
import com.ykb.payments.bill.transaction.institution.domain.InstitutionChannelProcess;
import com.ykb.payments.bill.transaction.institution.dto.ChannelDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionChannelDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionChannelProcessDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionProcessDTO;
import com.ykb.payments.bill.transaction.institution.repository.InstitutionChannelProcessRepository;

import java.time.LocalDateTime;
import java.util.Optional;
import java.util.List;
import java.util.Arrays;

class AdminInstitutionChnlProccesServiceImplTest {

    @Mock
    private InstitutionChannelProcessRepository institutionChannelProcessRepository;

    @Mock
    private AdminInstitutionChannelProcessMapper institutionChannelProcessMapper;

    @Mock
    private AdminInstitutionChannelService institutionChannelService;

    @Mock
    private AdminInstitutionProcessService institutionProcessService;

    @InjectMocks
    private AdminInstitutionChnlProccesServiceImpl adminInstitutionChnlProccesService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getAllInstitutionChnlProcceses_shouldReturnListOfInstitutionChnlProcessWebDTO() {
        List<Object[]> insChnnlProcessListOjectArray = Arrays.asList(new Object[]{}, new Object[]{});
        List<InstitutionChnlProcessWebDTO> expectedDTOs = Arrays.asList(new InstitutionChnlProcessWebDTO(), new InstitutionChnlProcessWebDTO());

        when(institutionChannelProcessRepository.findInstitutionChnlProcessWithInstitution()).thenReturn(insChnnlProcessListOjectArray);
        when(institutionChannelProcessMapper.objectArrayToWebDTO(any())).thenReturn(expectedDTOs.get(0), expectedDTOs.get(1));

        List<InstitutionChnlProcessWebDTO> result = adminInstitutionChnlProccesService.getAllInstitutionChnlProcceses();

        assertNotNull(result);
        assertEquals(2, result.size());
        verify(institutionChannelProcessRepository, times(1)).findInstitutionChnlProcessWithInstitution();
        verify(institutionChannelProcessMapper, times(2)).objectArrayToWebDTO(any());
    }

    @Test
    void getInstitutionChannelProccesById_shouldReturnInstitutionChannelProcessDTO_whenFound() {
        Long id = 1L;
        InstitutionChannelProcess institutionChannelProcess = new InstitutionChannelProcess();
        InstitutionChannelProcessDTO expectedDTO = new InstitutionChannelProcessDTO();

        when(institutionChannelProcessRepository.findById(id)).thenReturn(Optional.of(institutionChannelProcess));
        when(institutionChannelProcessMapper.toDTO(institutionChannelProcess)).thenReturn(expectedDTO);

        InstitutionChannelProcessDTO result = adminInstitutionChnlProccesService.getInstitutionChannelProccesById(id);

        assertNotNull(result);
        assertEquals(expectedDTO, result);
        verify(institutionChannelProcessRepository, times(1)).findById(id);
        verify(institutionChannelProcessMapper, times(1)).toDTO(institutionChannelProcess);
    }

    @Test
    void getInstitutionChannelProccesById_shouldReturnNull_whenNotFound() {
        Long id = 1L;

        when(institutionChannelProcessRepository.findById(id)).thenReturn(Optional.empty());

        InstitutionChannelProcessDTO result = adminInstitutionChnlProccesService.getInstitutionChannelProccesById(id);

        assertNull(result);
        verify(institutionChannelProcessRepository, times(1)).findById(id);
    }

    @Test
    void createInstitutionChannelProcces_shouldThrowDataConflictException_whenInstitutionChannelProcessAlreadyExists() {
        CreateInstitutionChannelProcessRequestDTO requestDTO = new CreateInstitutionChannelProcessRequestDTO();
        requestDTO.setInstitutionChannelId(1L);
        requestDTO.setInstitutionProcessId(2L);

        when(institutionChannelProcessRepository.existsByInstitutionChannelIdAndInstitutionProcessId(requestDTO.getInstitutionChannelId(), requestDTO.getInstitutionProcessId()))
                .thenReturn(true);

        assertThrows(DataConflictException.class, () -> adminInstitutionChnlProccesService.createInstitutionChannelProcces(requestDTO));
        verify(institutionChannelProcessRepository, times(1)).existsByInstitutionChannelIdAndInstitutionProcessId(requestDTO.getInstitutionChannelId(), requestDTO.getInstitutionProcessId());
    }

    @Test
    void createInstitutionChannelProcces_shouldThrowDataNotFoundException_whenInstitutionProcessNotFound() throws MicroException {
        CreateInstitutionChannelProcessRequestDTO requestDTO = new CreateInstitutionChannelProcessRequestDTO();
        requestDTO.setInstitutionChannelId(1L);
        requestDTO.setInstitutionProcessId(2L);

        when(institutionChannelProcessRepository.existsByInstitutionChannelIdAndInstitutionProcessId(requestDTO.getInstitutionChannelId(), requestDTO.getInstitutionProcessId()))
                .thenReturn(false);
        when(institutionProcessService.getInstitutionProcessById(requestDTO.getInstitutionProcessId())).thenReturn(null);

        assertThrows(DataNotFoundException.class, () -> adminInstitutionChnlProccesService.createInstitutionChannelProcces(requestDTO));
        verify(institutionProcessService, times(1)).getInstitutionProcessById(requestDTO.getInstitutionProcessId());
    }

    @Test
    void createInstitutionChannelProcces_shouldReturnInstitutionChannelProcessDTO_whenSuccessful() throws MicroException {
        CreateInstitutionChannelProcessRequestDTO requestDTO = new CreateInstitutionChannelProcessRequestDTO();
        requestDTO.setInstitutionChannelId(1L);
        requestDTO.setInstitutionProcessId(2L);
        InstitutionProcessDTO institutionProcessDTO = new InstitutionProcessDTO();
        InstitutionChannelDTO institutionChannelDTO = new InstitutionChannelDTO();
        InstitutionChannelProcessDTO institutionChannelProcessDTO = new InstitutionChannelProcessDTO();
        InstitutionChannelProcess institutionChannelProcess = new InstitutionChannelProcess();

        when(institutionChannelProcessRepository.existsByInstitutionChannelIdAndInstitutionProcessId(requestDTO.getInstitutionChannelId(), requestDTO.getInstitutionProcessId()))
                .thenReturn(false);
        when(institutionProcessService.getInstitutionProcessById(requestDTO.getInstitutionProcessId())).thenReturn(institutionProcessDTO);
        when(institutionChannelService.getInstitutionChannelById(requestDTO.getInstitutionChannelId())).thenReturn(institutionChannelDTO);
        when(institutionChannelProcessMapper.toDTO(requestDTO)).thenReturn(institutionChannelProcessDTO);
        when(institutionChannelProcessMapper.toEntity(institutionChannelProcessDTO)).thenReturn(institutionChannelProcess);
        when(institutionChannelProcessRepository.save(institutionChannelProcess)).thenReturn(institutionChannelProcess);
        when(institutionChannelProcessMapper.toDTO(institutionChannelProcess)).thenReturn(institutionChannelProcessDTO);

        InstitutionChannelProcessDTO result = adminInstitutionChnlProccesService.createInstitutionChannelProcces(requestDTO);

        assertNotNull(result);
        assertEquals(institutionChannelProcessDTO, result);
        verify(institutionChannelProcessRepository, times(1)).existsByInstitutionChannelIdAndInstitutionProcessId(requestDTO.getInstitutionChannelId(), requestDTO.getInstitutionProcessId());
        verify(institutionProcessService, times(1)).getInstitutionProcessById(requestDTO.getInstitutionProcessId());
        verify(institutionChannelService, times(1)).getInstitutionChannelById(requestDTO.getInstitutionChannelId());
        verify(institutionChannelProcessMapper, times(1)).toDTO(requestDTO);
        verify(institutionChannelProcessMapper, times(1)).toEntity(institutionChannelProcessDTO);
        verify(institutionChannelProcessRepository, times(1)).save(institutionChannelProcess);
        verify(institutionChannelProcessMapper, times(1)).toDTO(institutionChannelProcess);
    }

    @Test
    void updateInstitutionChannelProcces_shouldThrowDataNotFoundException_whenInstitutionChannelProcessDoesNotExist() {
        UpdateInstitutionChannelProcessRequestDTO requestDTO = new UpdateInstitutionChannelProcessRequestDTO();
        requestDTO.setId(1L);

        when(institutionChannelProcessRepository.findById(requestDTO.getId())).thenReturn(Optional.empty());

        assertThrows(DataNotFoundException.class, () -> adminInstitutionChnlProccesService.updateInstitutionChannelProcces(requestDTO));
        verify(institutionChannelProcessRepository, times(1)).findById(requestDTO.getId());
    }

    @Test
    void updateInstitutionChannelProcces_shouldReturnUpdatedInstitutionChannelProcessDTO_whenSuccessful() throws MicroException {
        UpdateInstitutionChannelProcessRequestDTO requestDTO = new UpdateInstitutionChannelProcessRequestDTO();
        requestDTO.setId(1L);
        requestDTO.setInstitutionChannelId(1L);
        requestDTO.setInstitutionProcessId(2L);
        requestDTO.setWorkingStartTime("09:00");
        requestDTO.setWorkingFinishTime("18:00");
        requestDTO.setIsActive(true);
        requestDTO.setUpdateUser("user");

        InstitutionChannelProcess existingInstitutionChannelProcess = new InstitutionChannelProcess();
        InstitutionChannelProcessDTO existingInstitutionChannelProcessDTO = new InstitutionChannelProcessDTO();
        InstitutionProcessDTO institutionProcessDTO = new InstitutionProcessDTO();
        InstitutionChannelDTO institutionChannelDTO = new InstitutionChannelDTO();
        InstitutionChannelProcess updatedInstitutionChannelProcess = new InstitutionChannelProcess();
        InstitutionChannelProcessDTO updatedInstitutionChannelProcessDTO = new InstitutionChannelProcessDTO();

        when(institutionChannelProcessRepository.findById(requestDTO.getId())).thenReturn(Optional.of(existingInstitutionChannelProcess));
        when(institutionProcessService.getInstitutionProcessById(requestDTO.getInstitutionProcessId())).thenReturn(institutionProcessDTO);
        when(institutionChannelService.getInstitutionChannelById(requestDTO.getInstitutionChannelId())).thenReturn(institutionChannelDTO);
        when(institutionChannelProcessMapper.toDTO(existingInstitutionChannelProcess)).thenReturn(existingInstitutionChannelProcessDTO);
        when(institutionChannelProcessMapper.toEntity(existingInstitutionChannelProcessDTO)).thenReturn(updatedInstitutionChannelProcess);
        when(institutionChannelProcessRepository.save(updatedInstitutionChannelProcess)).thenReturn(updatedInstitutionChannelProcess);
        when(institutionChannelProcessMapper.toDTO(updatedInstitutionChannelProcess)).thenReturn(updatedInstitutionChannelProcessDTO);

        InstitutionChannelProcessDTO result = adminInstitutionChnlProccesService.updateInstitutionChannelProcces(requestDTO);

        assertNotNull(result);
        assertEquals(updatedInstitutionChannelProcessDTO, result);
        verify(institutionChannelProcessRepository, times(1)).findById(requestDTO.getId());
        verify(institutionProcessService, times(1)).getInstitutionProcessById(requestDTO.getInstitutionProcessId());
        verify(institutionChannelService, times(1)).getInstitutionChannelById(requestDTO.getInstitutionChannelId());
        verify(institutionChannelProcessMapper, times(1)).toDTO(existingInstitutionChannelProcess);
        verify(institutionChannelProcessMapper, times(1)).toEntity(existingInstitutionChannelProcessDTO);
        verify(institutionChannelProcessRepository, times(1)).save(updatedInstitutionChannelProcess);
        verify(institutionChannelProcessMapper, times(1)).toDTO(updatedInstitutionChannelProcess);
    }
}
