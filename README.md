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
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionDebtTypeMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionDebtTypeRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionDebtTypeRequestDTO;
import com.ykb.payments.bill.transaction.institution.domain.InstitutionDebtType;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionDebtTypeDTO;
import com.ykb.payments.bill.transaction.institution.repository.InstitutionDebtTypeRepository;

import java.time.LocalDateTime;
import java.util.List;
import java.util.Optional;
import java.util.Arrays;

class AdminInstitutionDebtTypeServiceImplTest {

    @Mock
    private InstitutionDebtTypeRepository institutionDebtTypeRepository;

    @Mock
    private AdminInstitutionDebtTypeMapper institutionDebtTypeMapper;

    @Mock
    private AdminInstitutionService institutionService;

    @InjectMocks
    private AdminInstitutionDebtTypeServiceImpl adminInstitutionDebtTypeService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getAllInstitutionDebtTypes_shouldReturnListOfInstitutionDebtTypeDTO() {
        List<InstitutionDebtType> institutionDebtTypeList = Arrays.asList(new InstitutionDebtType(), new InstitutionDebtType());
        List<InstitutionDebtTypeDTO> expectedDTOs = Arrays.asList(new InstitutionDebtTypeDTO(), new InstitutionDebtTypeDTO());

        when(institutionDebtTypeRepository.findAll()).thenReturn(institutionDebtTypeList);
        when(institutionDebtTypeMapper.toDTOList(institutionDebtTypeList)).thenReturn(expectedDTOs);

        List<InstitutionDebtTypeDTO> result = adminInstitutionDebtTypeService.getAllInstitutionDebtTypes();

        assertNotNull(result);
        assertEquals(2, result.size());
        verify(institutionDebtTypeRepository, times(1)).findAll();
        verify(institutionDebtTypeMapper, times(1)).toDTOList(institutionDebtTypeList);
    }

    @Test
    void getInstitutionDebtTypeById_shouldReturnInstitutionDebtTypeDTO_whenFound() throws MicroException {
        Long id = 1L;
        InstitutionDebtType institutionDebtType = new InstitutionDebtType();
        InstitutionDebtTypeDTO expectedDTO = new InstitutionDebtTypeDTO();

        when(institutionDebtTypeRepository.findById(id)).thenReturn(Optional.of(institutionDebtType));
        when(institutionDebtTypeMapper.toDTO(institutionDebtType)).thenReturn(expectedDTO);

        InstitutionDebtTypeDTO result = adminInstitutionDebtTypeService.getInstitutionDebtTypeById(id);

        assertNotNull(result);
        assertEquals(expectedDTO, result);
        verify(institutionDebtTypeRepository, times(1)).findById(id);
        verify(institutionDebtTypeMapper, times(1)).toDTO(institutionDebtType);
    }

    @Test
    void getInstitutionDebtTypeById_shouldReturnNull_whenNotFound() throws MicroException {
        Long id = 1L;

        when(institutionDebtTypeRepository.findById(id)).thenReturn(Optional.empty());

        InstitutionDebtTypeDTO result = adminInstitutionDebtTypeService.getInstitutionDebtTypeById(id);

        assertNull(result);
        verify(institutionDebtTypeRepository, times(1)).findById(id);
    }

    @Test
    void createInstitutionDebtType_shouldThrowDataConflictException_whenInstitutionDebtTypeAlreadyExists() {
        CreateInstitutionDebtTypeRequestDTO requestDTO = new CreateInstitutionDebtTypeRequestDTO();
        requestDTO.setInstitutionId(1L);
        requestDTO.setDebtType("DEBT_TYPE");

        when(institutionDebtTypeRepository.findByInstitutionIdAndDebtTypeCode(requestDTO.getInstitutionId(), requestDTO.getDebtType()))
                .thenReturn(Optional.of(new InstitutionDebtType()));

        assertThrows(DataConflictException.class, () -> adminInstitutionDebtTypeService.createInstitutionDebtType(requestDTO));
        verify(institutionDebtTypeRepository, times(1)).findByInstitutionIdAndDebtTypeCode(requestDTO.getInstitutionId(), requestDTO.getDebtType());
    }

    @Test
    void createInstitutionDebtType_shouldThrowDataNotFoundException_whenInstitutionNotFound() throws MicroException {
        CreateInstitutionDebtTypeRequestDTO requestDTO = new CreateInstitutionDebtTypeRequestDTO();
        requestDTO.setInstitutionId(1L);

        when(institutionDebtTypeRepository.findByInstitutionIdAndDebtTypeCode(requestDTO.getInstitutionId(), requestDTO.getDebtType()))
                .thenReturn(Optional.empty());
        when(institutionService.getInstitutionByIdTypeSecond(requestDTO.getInstitutionId())).thenReturn(null);

        assertThrows(DataNotFoundException.class, () -> adminInstitutionDebtTypeService.createInstitutionDebtType(requestDTO));
        verify(institutionService, times(1)).getInstitutionByIdTypeSecond(requestDTO.getInstitutionId());
    }

    @Test
    void createInstitutionDebtType_shouldReturnInstitutionDebtTypeDTO_whenSuccessful() throws MicroException {
        CreateInstitutionDebtTypeRequestDTO requestDTO = new CreateInstitutionDebtTypeRequestDTO();
        requestDTO.setInstitutionId(1L);
        requestDTO.setDebtType("DEBT_TYPE");
        InstitutionDTO institutionDTO = new InstitutionDTO();
        InstitutionDebtTypeDTO institutionDebtTypeDTO = new InstitutionDebtTypeDTO();
        InstitutionDebtType institutionDebtType = new InstitutionDebtType();

        when(institutionDebtTypeRepository.findByInstitutionIdAndDebtTypeCode(requestDTO.getInstitutionId(), requestDTO.getDebtType()))
                .thenReturn(Optional.empty());
        when(institutionService.getInstitutionByIdTypeSecond(requestDTO.getInstitutionId())).thenReturn(institutionDTO);
        when(institutionDebtTypeMapper.toDTO(requestDTO)).thenReturn(institutionDebtTypeDTO);
        when(institutionDebtTypeMapper.toEntity(institutionDebtTypeDTO)).thenReturn(institutionDebtType);
        when(institutionDebtTypeRepository.save(institutionDebtType)).thenReturn(institutionDebtType);
        when(institutionDebtTypeMapper.toDTO(institutionDebtType)).thenReturn(institutionDebtTypeDTO);

        InstitutionDebtTypeDTO result = adminInstitutionDebtTypeService.createInstitutionDebtType(requestDTO);

        assertNotNull(result);
        assertEquals(institutionDebtTypeDTO, result);
        verify(institutionDebtTypeRepository, times(1)).findByInstitutionIdAndDebtTypeCode(requestDTO.getInstitutionId(), requestDTO.getDebtType());
        verify(institutionService, times(1)).getInstitutionByIdTypeSecond(requestDTO.getInstitutionId());
        verify(institutionDebtTypeMapper, times(1)).toDTO(requestDTO);
        verify(institutionDebtTypeMapper, times(1)).toEntity(institutionDebtTypeDTO);
        verify(institutionDebtTypeRepository, times(1)).save(institutionDebtType);
        verify(institutionDebtTypeMapper, times(1)).toDTO(institutionDebtType);
    }

    @Test
    void updateInstitutionDebtType_shouldThrowDataNotFoundException_whenInstitutionDebtTypeNotFound() throws MicroException {
        UpdateInstitutionDebtTypeRequestDTO requestDTO = new UpdateInstitutionDebtTypeRequestDTO();
        requestDTO.setId(1L);

        when(institutionDebtTypeRepository.findById(requestDTO.getId())).thenReturn(Optional.empty());

        assertThrows(DataNotFoundException.class, () -> adminInstitutionDebtTypeService.updateInstitutionDebtType(requestDTO));
        verify(institutionDebtTypeRepository, times(1)).findById(requestDTO.getId());
    }

    @Test
    void updateInstitutionDebtType_shouldReturnUpdatedInstitutionDebtTypeDTO_whenSuccessful() throws MicroException {
        UpdateInstitutionDebtTypeRequestDTO requestDTO = new UpdateInstitutionDebtTypeRequestDTO();
        requestDTO.setId(1L);
        requestDTO.setInstitutionId(1L);
        requestDTO.setDebtType("DEBT_TYPE");
        requestDTO.setUpdateUser("user");
        requestDTO.setIsActive(true);
        requestDTO.setExplanation("Explanation");

        InstitutionDebtType existingInstitutionDebtType = new InstitutionDebtType();
        InstitutionDebtTypeDTO existingInstitutionDebtTypeDTO = new InstitutionDebtTypeDTO();
        InstitutionDTO institutionDTO = new InstitutionDTO();
        InstitutionDebtType updatedInstitutionDebtType = new InstitutionDebtType();
        InstitutionDebtTypeDTO updatedInstitutionDebtTypeDTO = new InstitutionDebtTypeDTO();

        when(institutionDebtTypeRepository.findById(requestDTO.getId())).thenReturn(Optional.of(existingInstitutionDebtType));
        when(institutionService.getInstitutionByIdTypeSecond(requestDTO.getInstitutionId())).thenReturn(institutionDTO);
        when(institutionDebtTypeMapper.toDTO(existingInstitutionDebtType)).thenReturn(existingInstitutionDebtTypeDTO);
        when(institutionDebtTypeMapper.toEntity(existingInstitutionDebtTypeDTO)).thenReturn(updatedInstitutionDebtType);
        when(institutionDebtTypeRepository.save(updatedInstitutionDebtType)).thenReturn(updatedInstitutionDebtType);
        when(institutionDebtTypeMapper.toDTO(updatedInstitutionDebtType)).thenReturn(updatedInstitutionDebtTypeDTO);

        InstitutionDebtTypeDTO result = adminInstitutionDebtTypeService.updateInstitutionDebtType(requestDTO);

        assertNotNull(result);
        assertEquals(updatedInstitutionDebtTypeDTO, result);
        verify(institutionDebtTypeRepository, times(1)).findById(requestDTO.getId());
        verify(institutionService, times(1)).getInstitutionByIdTypeSecond(requestDTO.getInstitutionId());
        verify(institutionDebtTypeMapper, times(1)).toDTO(existingInstitutionDebtType);
        verify(institutionDebtTypeMapper, times(1)).toEntity(existingInstitutionDebtTypeDTO);
        verify(institutionDebtTypeRepository, times(1)).save(updatedInstitutionDebtType);
        verify(institutionDebtTypeMapper, times(1)).toDTO(updatedInstitutionDebtType);
    }
}
