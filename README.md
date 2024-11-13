import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import com.ykb.architecture.micro.error.exception.DataConflictException;
import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.common.exception.BillExceptionsUI;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionProcessMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionService;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminProcessService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionProcessRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionProcessRequestDTO;
import com.ykb.payments.bill.transaction.institution.domain.InstitutionProcess;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionProcessDTO;
import com.ykb.payments.bill.transaction.institution.dto.ProcessDTO;
import com.ykb.payments.bill.transaction.institution.repository.InstitutionProcessRepository;

import java.time.LocalDateTime;
import java.util.List;
import java.util.Optional;
import java.util.Arrays;

class AdminInstitutionProcessServiceImplTest {

    @Mock
    private InstitutionProcessRepository institutionProcessRepository;

    @Mock
    private AdminInstitutionProcessMapper institutionProcessMapper;

    @Mock
    private AdminInstitutionService institutionService;

    @Mock
    private AdminProcessService processService;

    @InjectMocks
    private AdminInstitutionProcessServiceImpl adminInstitutionProcessService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getAllInstitutionProcess_shouldReturnListOfInstitutionProcessDTO() {
        List<InstitutionProcess> institutionProcessList = Arrays.asList(new InstitutionProcess(), new InstitutionProcess());
        List<InstitutionProcessDTO> expectedDTOs = Arrays.asList(new InstitutionProcessDTO(), new InstitutionProcessDTO());

        when(institutionProcessRepository.findAll()).thenReturn(institutionProcessList);
        when(institutionProcessMapper.toDTOList(institutionProcessList)).thenReturn(expectedDTOs);

        List<InstitutionProcessDTO> result = adminInstitutionProcessService.getAllInstitutionProcess();

        assertNotNull(result);
        assertEquals(2, result.size());
        verify(institutionProcessRepository, times(1)).findAll();
        verify(institutionProcessMapper, times(1)).toDTOList(institutionProcessList);
    }

    @Test
    void getInstitutionProcessById_shouldReturnInstitutionProcessDTO_whenFound() {
        Long id = 1L;
        InstitutionProcess institutionProcess = new InstitutionProcess();
        InstitutionProcessDTO expectedDTO = new InstitutionProcessDTO();

        when(institutionProcessRepository.findById(id)).thenReturn(Optional.of(institutionProcess));
        when(institutionProcessMapper.toDTO(institutionProcess)).thenReturn(expectedDTO);

        InstitutionProcessDTO result = adminInstitutionProcessService.getInstitutionProcessById(id);

        assertNotNull(result);
        assertEquals(expectedDTO, result);
        verify(institutionProcessRepository, times(1)).findById(id);
        verify(institutionProcessMapper, times(1)).toDTO(institutionProcess);
    }

    @Test
    void getInstitutionProcessById_shouldReturnNull_whenNotFound() {
        Long id = 1L;

        when(institutionProcessRepository.findById(id)).thenReturn(Optional.empty());

        InstitutionProcessDTO result = adminInstitutionProcessService.getInstitutionProcessById(id);

        assertNull(result);
        verify(institutionProcessRepository, times(1)).findById(id);
    }

    @Test
    void createInstitutionProcess_shouldThrowDataConflictException_whenInstitutionProcessAlreadyExists() {
        CreateInstitutionProcessRequestDTO requestDTO = new CreateInstitutionProcessRequestDTO();
        requestDTO.setInstitutionId(1L);
        requestDTO.setProcess("PROCESS_CODE");

        when(institutionProcessRepository.existsByInstitutionIdAndProcessCode(requestDTO.getInstitutionId(), requestDTO.getProcess()))
                .thenReturn(true);

        assertThrows(DataConflictException.class, () -> adminInstitutionProcessService.createInstitutionProcess(requestDTO));
        verify(institutionProcessRepository, times(1)).existsByInstitutionIdAndProcessCode(requestDTO.getInstitutionId(), requestDTO.getProcess());
    }

    @Test
    void createInstitutionProcess_shouldThrowDataConflictException_whenInstitutionNotFound() throws MicroException {
        CreateInstitutionProcessRequestDTO requestDTO = new CreateInstitutionProcessRequestDTO();
        requestDTO.setInstitutionId(1L);

        when(institutionProcessRepository.existsByInstitutionIdAndProcessCode(requestDTO.getInstitutionId(), requestDTO.getProcess()))
                .thenReturn(false);
        when(institutionService.getInstitutionByIdTypeSecond(requestDTO.getInstitutionId())).thenReturn(null);

        assertThrows(DataConflictException.class, () -> adminInstitutionProcessService.createInstitutionProcess(requestDTO));
        verify(institutionService, times(1)).getInstitutionByIdTypeSecond(requestDTO.getInstitutionId());
    }

    @Test
    void createInstitutionProcess_shouldReturnInstitutionProcessDTO_whenSuccessful() throws MicroException {
        CreateInstitutionProcessRequestDTO requestDTO = new CreateInstitutionProcessRequestDTO();
        requestDTO.setInstitutionId(1L);
        requestDTO.setProcess("PROCESS_CODE");
        InstitutionDTO institutionDTO = new InstitutionDTO();
        ProcessDTO processDTO = new ProcessDTO();
        InstitutionProcessDTO institutionProcessDTO = new InstitutionProcessDTO();
        InstitutionProcess institutionProcess = new InstitutionProcess();

        when(institutionProcessRepository.existsByInstitutionIdAndProcessCode(requestDTO.getInstitutionId(), requestDTO.getProcess()))
                .thenReturn(false);
        when(institutionService.getInstitutionByIdTypeSecond(requestDTO.getInstitutionId())).thenReturn(institutionDTO);
        when(processService.getProcessByCode(requestDTO.getProcess())).thenReturn(processDTO);
        when(institutionProcessMapper.toDTO(requestDTO)).thenReturn(institutionProcessDTO);
        when(institutionProcessMapper.toEntity(institutionProcessDTO)).thenReturn(institutionProcess);
        when(institutionProcessRepository.save(institutionProcess)).thenReturn(institutionProcess);
        when(institutionProcessMapper.toDTO(institutionProcess)).thenReturn(institutionProcessDTO);

        InstitutionProcessDTO result = adminInstitutionProcessService.createInstitutionProcess(requestDTO);

        assertNotNull(result);
        assertEquals(institutionProcessDTO, result);
        verify(institutionProcessRepository, times(1)).existsByInstitutionIdAndProcessCode(requestDTO.getInstitutionId(), requestDTO.getProcess());
        verify(institutionService, times(1)).getInstitutionByIdTypeSecond(requestDTO.getInstitutionId());
        verify(processService, times(1)).getProcessByCode(requestDTO.getProcess());
        verify(institutionProcessMapper, times(1)).toDTO(requestDTO);
        verify(institutionProcessMapper, times(1)).toEntity(institutionProcessDTO);
        verify(institutionProcessRepository, times(1)).save(institutionProcess);
        verify(institutionProcessMapper, times(1)).toDTO(institutionProcess);
    }

    @Test
    void updateInstitutionProcess_shouldThrowDataConflictException_whenInstitutionProcessNotFound() {
        UpdateInstitutionProcessRequestDTO requestDTO = new UpdateInstitutionProcessRequestDTO();
        requestDTO.setId(1L);

        when(institutionProcessRepository.findById(requestDTO.getId())).thenReturn(Optional.empty());

        assertThrows(DataConflictException.class, () -> adminInstitutionProcessService.updateInstitutionProcess(requestDTO));
        verify(institutionProcessRepository, times(1)).findById(requestDTO.getId());
    }

    @Test
    void updateInstitutionProcess_shouldReturnUpdatedInstitutionProcessDTO_whenSuccessful() throws MicroException {
        UpdateInstitutionProcessRequestDTO requestDTO = new UpdateInstitutionProcessRequestDTO();
        requestDTO.setId(1L);
        requestDTO.setInstitutionId(1L);
        requestDTO.setProcess("PROCESS_CODE");
        requestDTO.setIsOnline(true);
        requestDTO.setClassName("ClassName");
        requestDTO.setIsActive(true);
        requestDTO.setUpdateUser("user");

        InstitutionProcess existingInstitutionProcess = new InstitutionProcess();
        InstitutionProcessDTO existingInstitutionProcessDTO = new InstitutionProcessDTO();
        InstitutionDTO institutionDTO = new InstitutionDTO();
        ProcessDTO processDTO = new ProcessDTO();
        InstitutionProcess updatedInstitutionProcess = new InstitutionProcess();
        InstitutionProcessDTO updatedInstitutionProcessDTO = new InstitutionProcessDTO();

        when(institutionProcessRepository.findById(requestDTO.getId())).thenReturn(Optional.of(existingInstitutionProcess));
        when(institutionService.getInstitutionByIdTypeSecond(requestDTO.getInstitutionId())).thenReturn(institutionDTO);
        when(processService.getProcessByCode(requestDTO.getProcess())).thenReturn(processDTO);
        when(institutionProcessMapper.toDTO(existingInstitutionProcess)).thenReturn(existingInstitutionProcessDTO);
        when(institutionProcessMapper.toEntity(existingInstitutionProcessDTO)).thenReturn(updatedInstitutionProcess);
        when(institutionProcessRepository.save(updatedInstitutionProcess)).thenReturn(updatedInstitutionProcess);
        when(institutionProcessMapper.toDTO(updatedInstitutionProcess)).thenReturn(updatedInstitutionProcessDTO);

        InstitutionProcessDTO result = adminInstitutionProcessService.updateInstitutionProcess(requestDTO);

        assertNotNull(result);
        assertEquals(updatedInstitutionProcessDTO, result);
        verify(institutionProcessRepository, times(1)).findById(requestDTO.getId());
        verify(institutionService, times(1)).getInstitutionByIdTypeSecond(requestDTO.getInstitutionId());
        verify(processService, times(1)).getProcessByCode(requestDTO.getProcess());
        verify(institutionProcessMapper, times(1)).toDTO(existingInstitutionProcess);
        verify(institutionProcessMapper, times(1)).toEntity(existingInstitutionProcessDTO);
        verify(institutionProcessRepository, times(1)).save(updatedInstitutionProcess);
        verify(institutionProcessMapper, times(1)).toDTO(updatedInstitutionProcess);
    }
}
