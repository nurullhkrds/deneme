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
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionFeatureMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminFeatureService;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionFeatureRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionFeatureRequestDTO;
import com.ykb.payments.bill.transaction.institution.domain.InstitutionFeature;
import com.ykb.payments.bill.transaction.institution.dto.FeatureDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionFeatureDTO;
import com.ykb.payments.bill.transaction.institution.repository.InstitutionFeatureRepository;

import java.time.LocalDateTime;
import java.util.List;
import java.util.Optional;
import java.util.Arrays;

class AdminInstitutionFeatureServiceImplTest {

    @Mock
    private InstitutionFeatureRepository institutionFeatureRepository;

    @Mock
    private AdminInstitutionFeatureMapper institutionFeatureMapper;

    @Mock
    private AdminInstitutionService institutionService;

    @Mock
    private AdminFeatureService featureService;

    @InjectMocks
    private AdminInstitutionFeatureServiceImpl adminInstitutionFeatureService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getAllInstitutionFeatures_shouldReturnListOfInstitutionFeatureDTO() {
        List<InstitutionFeature> institutionFeatureList = Arrays.asList(new InstitutionFeature(), new InstitutionFeature());
        List<InstitutionFeatureDTO> expectedDTOs = Arrays.asList(new InstitutionFeatureDTO(), new InstitutionFeatureDTO());

        when(institutionFeatureRepository.findAll()).thenReturn(institutionFeatureList);
        when(institutionFeatureMapper.toInstitutionFeatureDTOList(institutionFeatureList)).thenReturn(expectedDTOs);

        List<InstitutionFeatureDTO> result = adminInstitutionFeatureService.getAllInstitutionFeatures();

        assertNotNull(result);
        assertEquals(2, result.size());
        verify(institutionFeatureRepository, times(1)).findAll();
        verify(institutionFeatureMapper, times(1)).toInstitutionFeatureDTOList(institutionFeatureList);
    }

    @Test
    void getInstitutionFeatureById_shouldReturnInstitutionFeatureDTO_whenFound() throws MicroException {
        Long id = 1L;
        InstitutionFeature institutionFeature = new InstitutionFeature();
        InstitutionFeatureDTO expectedDTO = new InstitutionFeatureDTO();

        when(institutionFeatureRepository.findById(id)).thenReturn(Optional.of(institutionFeature));
        when(institutionFeatureMapper.toInstitutionFeatureDTO(institutionFeature)).thenReturn(expectedDTO);

        InstitutionFeatureDTO result = adminInstitutionFeatureService.getInstitutionFeatureById(id);

        assertNotNull(result);
        assertEquals(expectedDTO, result);
        verify(institutionFeatureRepository, times(1)).findById(id);
        verify(institutionFeatureMapper, times(1)).toInstitutionFeatureDTO(institutionFeature);
    }

    @Test
    void getInstitutionFeatureById_shouldReturnNull_whenNotFound() throws MicroException {
        Long id = 1L;

        when(institutionFeatureRepository.findById(id)).thenReturn(Optional.empty());

        InstitutionFeatureDTO result = adminInstitutionFeatureService.getInstitutionFeatureById(id);

        assertNull(result);
        verify(institutionFeatureRepository, times(1)).findById(id);
    }

    @Test
    void createInstitutionFeature_shouldThrowDataConflictException_whenInstitutionFeatureAlreadyExists() {
        CreateInstitutionFeatureRequestDTO requestDTO = new CreateInstitutionFeatureRequestDTO();
        requestDTO.setInstitutionId(1L);
        requestDTO.setFeatureCode(() -> "FEATURE_CODE");

        when(institutionFeatureRepository.findByInstitutionIdAndFeatureCode(requestDTO.getInstitutionId(), requestDTO.getFeatureCode().getValue()))
                .thenReturn(Optional.of(new InstitutionFeature()));

        assertThrows(DataConflictException.class, () -> adminInstitutionFeatureService.createInstitutionFeature(requestDTO));
        verify(institutionFeatureRepository, times(1)).findByInstitutionIdAndFeatureCode(requestDTO.getInstitutionId(), requestDTO.getFeatureCode().getValue());
    }

    @Test
    void createInstitutionFeature_shouldThrowDataNotFoundException_whenInstitutionNotFound() throws MicroException {
        CreateInstitutionFeatureRequestDTO requestDTO = new CreateInstitutionFeatureRequestDTO();
        requestDTO.setInstitutionId(1L);

        when(institutionFeatureRepository.findByInstitutionIdAndFeatureCode(requestDTO.getInstitutionId(), requestDTO.getFeatureCode().getValue()))
                .thenReturn(Optional.empty());
        when(institutionService.getInstitutionByIdTypeSecond(requestDTO.getInstitutionId())).thenReturn(null);

        assertThrows(DataNotFoundException.class, () -> adminInstitutionFeatureService.createInstitutionFeature(requestDTO));
        verify(institutionService, times(1)).getInstitutionByIdTypeSecond(requestDTO.getInstitutionId());
    }

    @Test
    void createInstitutionFeature_shouldReturnInstitutionFeatureDTO_whenSuccessful() throws MicroException {
        CreateInstitutionFeatureRequestDTO requestDTO = new CreateInstitutionFeatureRequestDTO();
        requestDTO.setInstitutionId(1L);
        requestDTO.setFeatureCode(() -> "FEATURE_CODE");
        InstitutionDTO institutionDTO = new InstitutionDTO();
        FeatureDTO featureDTO = new FeatureDTO();
        InstitutionFeatureDTO institutionFeatureDTO = new InstitutionFeatureDTO();
        InstitutionFeature institutionFeature = new InstitutionFeature();

        when(institutionFeatureRepository.findByInstitutionIdAndFeatureCode(requestDTO.getInstitutionId(), requestDTO.getFeatureCode().getValue()))
                .thenReturn(Optional.empty());
        when(institutionService.getInstitutionByIdTypeSecond(requestDTO.getInstitutionId())).thenReturn(institutionDTO);
        when(featureService.getFeatureByCode(requestDTO.getFeatureCode())).thenReturn(featureDTO);
        when(institutionFeatureMapper.toInstitutionFeatureDTO(requestDTO)).thenReturn(institutionFeatureDTO);
        when(institutionFeatureMapper.toInstitutionFeature(institutionFeatureDTO)).thenReturn(institutionFeature);
        when(institutionFeatureRepository.save(institutionFeature)).thenReturn(institutionFeature);
        when(institutionFeatureMapper.toInstitutionFeatureDTO(institutionFeature)).thenReturn(institutionFeatureDTO);

        InstitutionFeatureDTO result = adminInstitutionFeatureService.createInstitutionFeature(requestDTO);

        assertNotNull(result);
        assertEquals(institutionFeatureDTO, result);
        verify(institutionFeatureRepository, times(1)).findByInstitutionIdAndFeatureCode(requestDTO.getInstitutionId(), requestDTO.getFeatureCode().getValue());
        verify(institutionService, times(1)).getInstitutionByIdTypeSecond(requestDTO.getInstitutionId());
        verify(featureService, times(1)).getFeatureByCode(requestDTO.getFeatureCode());
        verify(institutionFeatureMapper, times(1)).toInstitutionFeatureDTO(requestDTO);
        verify(institutionFeatureMapper, times(1)).toInstitutionFeature(institutionFeatureDTO);
        verify(institutionFeatureRepository, times(1)).save(institutionFeature);
        verify(institutionFeatureMapper, times(1)).toInstitutionFeatureDTO(institutionFeature);
    }

    @Test
    void updateInstitutionFeature_shouldThrowDataNotFoundException_whenInstitutionFeatureNotFound() throws MicroException {
        UpdateInstitutionFeatureRequestDTO requestDTO = new UpdateInstitutionFeatureRequestDTO();
        requestDTO.setId(1L);

        when(institutionFeatureRepository.findById(requestDTO.getId())).thenReturn(Optional.empty());

        assertThrows(DataNotFoundException.class, () -> adminInstitutionFeatureService.updateInstitutionFeature(requestDTO));
        verify(institutionFeatureRepository, times(1)).findById(requestDTO.getId());
    }

    @Test
    void updateInstitutionFeature_shouldReturnUpdatedInstitutionFeatureDTO_whenSuccessful() throws MicroException {
        UpdateInstitutionFeatureRequestDTO requestDTO = new UpdateInstitutionFeatureRequestDTO();
        requestDTO.setId(1L);
        requestDTO.setInstitutionId(1L);
        requestDTO.setFeatureCode(() -> "FEATURE_CODE");
        requestDTO.setFeatureValue("FeatureValue");
        requestDTO.setIsActive(true);
        requestDTO.setUpdateUser("user");

        InstitutionFeature existingInstitutionFeature = new InstitutionFeature();
        InstitutionFeatureDTO existingInstitutionFeatureDTO = new InstitutionFeatureDTO();
        InstitutionDTO institutionDTO = new InstitutionDTO();
        FeatureDTO featureDTO = new FeatureDTO();
        InstitutionFeature updatedInstitutionFeature = new InstitutionFeature();
        InstitutionFeatureDTO updatedInstitutionFeatureDTO = new InstitutionFeatureDTO();

        when(institutionFeatureRepository.findById(requestDTO.getId())).thenReturn(Optional.of(existingInstitutionFeature));
        when(institutionService.getInstitutionByIdTypeSecond(requestDTO.getInstitutionId())).thenReturn(institutionDTO);
        when(featureService.getFeatureByCode(requestDTO.getFeatureCode())).thenReturn(featureDTO);
        when(institutionFeatureMapper.toInstitutionFeatureDTO(existingInstitutionFeature)).thenReturn(existingInstitutionFeatureDTO);
        when(institutionFeatureMapper.toInstitutionFeature(existingInstitutionFeatureDTO)).thenReturn(updatedInstitutionFeature);
        when(institutionFeatureRepository.save(updatedInstitutionFeature)).thenReturn(updatedInstitutionFeature);
        when(institutionFeatureMapper.toInstitutionFeatureDTO(updatedInstitutionFeature)).thenReturn(updatedInstitutionFeatureDTO);

        InstitutionFeatureDTO result = adminInstitutionFeatureService.updateInstitutionFeature(requestDTO);

        assertNotNull(result);
        assertEquals(updatedInstitutionFeatureDTO, result);
        verify(institutionFeatureRepository, times(1)).findById(requestDTO.getId());
        verify(institutionService, times(1)).getInstitutionByIdTypeSecond(requestDTO.getInstitutionId());
        verify(featureService, times(1)).getFeatureByCode(requestDTO.getFeatureCode());
        verify(institutionFeatureMapper, times(1)).toInstitutionFeatureDTO(existingInstitutionFeature);
        verify(institutionFeatureMapper, times(1)).toInstitutionFeature(existingInstitutionFeatureDTO);
        verify(institutionFeatureRepository, times(1)).save(updatedInstitutionFeature);
        verify(institutionFeatureMapper, times(1)).toInstitutionFeatureDTO(updatedInstitutionFeature);
    }
}
