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
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionCityMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminCityService;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionCityRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionCityRequestDTO;
import com.ykb.payments.bill.transaction.institution.domain.InstitutionCity;
import com.ykb.payments.bill.transaction.institution.dto.CityDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionCityDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionDTO;
import com.ykb.payments.bill.transaction.institution.repository.InstitutionCityRepository;

import java.time.LocalDateTime;
import java.util.List;
import java.util.Optional;
import java.util.Arrays;

class AdminInstitutionCityServiceImplTest {

    @Mock
    private InstitutionCityRepository institutionCityRepository;

    @Mock
    private AdminInstitutionCityMapper institutionCityMapper;

    @Mock
    private AdminInstitutionService institutionService;

    @Mock
    private AdminCityService cityService;

    @InjectMocks
    private AdminInstitutionCityServiceImpl adminInstitutionCityService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getAllInstitutionCities_shouldReturnListOfInstitutionCityDTO() {
        List<InstitutionCity> institutionCityList = Arrays.asList(new InstitutionCity(), new InstitutionCity());
        List<InstitutionCityDTO> expectedDTOs = Arrays.asList(new InstitutionCityDTO(), new InstitutionCityDTO());

        when(institutionCityRepository.findAll()).thenReturn(institutionCityList);
        when(institutionCityMapper.toDTOList(institutionCityList)).thenReturn(expectedDTOs);

        List<InstitutionCityDTO> result = adminInstitutionCityService.getAllInstitutionCities();

        assertNotNull(result);
        assertEquals(2, result.size());
        verify(institutionCityRepository, times(1)).findAll();
        verify(institutionCityMapper, times(1)).toDTOList(institutionCityList);
    }

    @Test
    void getInstitutionCityById_shouldReturnInstitutionCityDTO_whenFound() {
        Long id = 1L;
        InstitutionCity institutionCity = new InstitutionCity();
        InstitutionCityDTO expectedDTO = new InstitutionCityDTO();

        when(institutionCityRepository.findById(id)).thenReturn(Optional.of(institutionCity));
        when(institutionCityMapper.toInstitutionCityDTO(institutionCity)).thenReturn(expectedDTO);

        InstitutionCityDTO result = adminInstitutionCityService.getInstitutionCityById(id);

        assertNotNull(result);
        assertEquals(expectedDTO, result);
        verify(institutionCityRepository, times(1)).findById(id);
        verify(institutionCityMapper, times(1)).toInstitutionCityDTO(institutionCity);
    }

    @Test
    void getInstitutionCityById_shouldReturnNull_whenNotFound() {
        Long id = 1L;

        when(institutionCityRepository.findById(id)).thenReturn(Optional.empty());

        InstitutionCityDTO result = adminInstitutionCityService.getInstitutionCityById(id);

        assertNull(result);
        verify(institutionCityRepository, times(1)).findById(id);
    }

    @Test
    void createInstitutionCity_shouldThrowDataConflictException_whenInstitutionCityAlreadyExists() {
        CreateInstitutionCityRequestDTO requestDTO = new CreateInstitutionCityRequestDTO();
        requestDTO.setInstitutionId(1L);
        requestDTO.setCode("CITY_CODE");

        when(institutionCityRepository.findByInstitutionIdAndCityCode(requestDTO.getInstitutionId(), requestDTO.getCode()))
                .thenReturn(Optional.of(new InstitutionCity()));

        assertThrows(DataConflictException.class, () -> adminInstitutionCityService.createInstitutionCity(requestDTO));
        verify(institutionCityRepository, times(1)).findByInstitutionIdAndCityCode(requestDTO.getInstitutionId(), requestDTO.getCode());
    }

    @Test
    void createInstitutionCity_shouldThrowDataConflictException_whenInstitutionNotFound() throws MicroException {
        CreateInstitutionCityRequestDTO requestDTO = new CreateInstitutionCityRequestDTO();
        requestDTO.setInstitutionId(1L);

        when(institutionCityRepository.findByInstitutionIdAndCityCode(requestDTO.getInstitutionId(), requestDTO.getCode()))
                .thenReturn(Optional.empty());
        when(institutionService.getInstitutionByIdTypeSecond(requestDTO.getInstitutionId())).thenReturn(null);

        assertThrows(DataConflictException.class, () -> adminInstitutionCityService.createInstitutionCity(requestDTO));
        verify(institutionService, times(1)).getInstitutionByIdTypeSecond(requestDTO.getInstitutionId());
    }

    @Test
    void createInstitutionCity_shouldReturnInstitutionCityDTO_whenSuccessful() throws MicroException {
        CreateInstitutionCityRequestDTO requestDTO = new CreateInstitutionCityRequestDTO();
        requestDTO.setInstitutionId(1L);
        requestDTO.setCode("CITY_CODE");
        InstitutionDTO institutionDTO = new InstitutionDTO();
        CityDTO cityDTO = new CityDTO();
        InstitutionCityDTO institutionCityDTO = new InstitutionCityDTO();
        InstitutionCity institutionCity = new InstitutionCity();

        when(institutionCityRepository.findByInstitutionIdAndCityCode(requestDTO.getInstitutionId(), requestDTO.getCode()))
                .thenReturn(Optional.empty());
        when(institutionService.getInstitutionByIdTypeSecond(requestDTO.getInstitutionId())).thenReturn(institutionDTO);
        when(cityService.getCityByCode(requestDTO.getCode())).thenReturn(cityDTO);
        when(institutionCityMapper.toDTO(requestDTO)).thenReturn(institutionCityDTO);
        when(institutionCityMapper.toInstitutionCity(institutionCityDTO)).thenReturn(institutionCity);
        when(institutionCityRepository.save(institutionCity)).thenReturn(institutionCity);
        when(institutionCityMapper.toInstitutionCityDTO(institutionCity)).thenReturn(institutionCityDTO);

        InstitutionCityDTO result = adminInstitutionCityService.createInstitutionCity(requestDTO);

        assertNotNull(result);
        assertEquals(institutionCityDTO, result);
        verify(institutionCityRepository, times(1)).findByInstitutionIdAndCityCode(requestDTO.getInstitutionId(), requestDTO.getCode());
        verify(institutionService, times(1)).getInstitutionByIdTypeSecond(requestDTO.getInstitutionId());
        verify(cityService, times(1)).getCityByCode(requestDTO.getCode());
        verify(institutionCityMapper, times(1)).toDTO(requestDTO);
        verify(institutionCityMapper, times(1)).toInstitutionCity(institutionCityDTO);
        verify(institutionCityRepository, times(1)).save(institutionCity);
        verify(institutionCityMapper, times(1)).toInstitutionCityDTO(institutionCity);
    }

    @Test
    void updateInstitutionCity_shouldThrowDataConflictException_whenInstitutionCityNotFound() {
        UpdateInstitutionCityRequestDTO requestDTO = new UpdateInstitutionCityRequestDTO();
        requestDTO.setId(1L);

        when(institutionCityRepository.findById(requestDTO.getId())).thenReturn(Optional.empty());

        assertThrows(DataConflictException.class, () -> adminInstitutionCityService.updateInstitutionCity(requestDTO));
        verify(institutionCityRepository, times(1)).findById(requestDTO.getId());
    }

    @Test
    void updateInstitutionCity_shouldReturnUpdatedInstitutionCityDTO_whenSuccessful() throws MicroException {
        UpdateInstitutionCityRequestDTO requestDTO = new UpdateInstitutionCityRequestDTO();
        requestDTO.setId(1L);
        requestDTO.setInstitutionId(1L);
        requestDTO.setCode("CITY_CODE");
        requestDTO.setUpdateUser("user");
        requestDTO.setIsActive(true);

        InstitutionCity existingInstitutionCity = new InstitutionCity();
        InstitutionCityDTO existingInstitutionCityDTO = new InstitutionCityDTO();
        InstitutionDTO institutionDTO = new InstitutionDTO();
        CityDTO cityDTO = new CityDTO();
        InstitutionCity updatedInstitutionCity = new InstitutionCity();
        InstitutionCityDTO updatedInstitutionCityDTO = new InstitutionCityDTO();

        when(institutionCityRepository.findById(requestDTO.getId())).thenReturn(Optional.of(existingInstitutionCity));
        when(institutionService.getInstitutionByIdTypeSecond(requestDTO.getInstitutionId())).thenReturn(institutionDTO);
        when(cityService.getCityByCode(requestDTO.getCode())).thenReturn(cityDTO);
        when(institutionCityMapper.toInstitutionCityDTO(existingInstitutionCity)).thenReturn(existingInstitutionCityDTO);
        when(institutionCityMapper.toInstitutionCity(existingInstitutionCityDTO)).thenReturn(updatedInstitutionCity);
        when(institutionCityRepository.save(updatedInstitutionCity)).thenReturn(updatedInstitutionCity);
        when(institutionCityMapper.toInstitutionCityDTO(updatedInstitutionCity)).thenReturn(updatedInstitutionCityDTO);

        InstitutionCityDTO result = adminInstitutionCityService.updateInstitutionCity(requestDTO);

        assertNotNull(result);
        assertEquals(updatedInstitutionCityDTO, result);
        verify(institutionCityRepository, times(1)).findById(requestDTO.getId());
        verify(institutionService, times(1)).getInstitutionByIdTypeSecond(requestDTO.getInstitutionId());
        verify(cityService, times(1)).getCityByCode(requestDTO.getCode());
        verify(institutionCityMapper, times(1)).toInstitutionCityDTO(existingInstitutionCity);
        verify(institutionCityMapper, times(1)).toInstitutionCity(existingInstitutionCityDTO);
        verify(institutionCityRepository, times(1)).save(updatedInstitutionCity);
        verify(institutionCityMapper, times(1)).toInstitutionCityDTO(updatedInstitutionCity);
    }
}
