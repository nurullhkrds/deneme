import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

class AdminCityServiceImplTest {

    @Mock
    private CityRepository cityRepository;

    @Mock
    private AdminCityMapper cityMapper;

    @InjectMocks
    private AdminCityServiceImpl adminCityService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getCityByCode_shouldReturnCityDTO_whenCityExists() throws MicroException {
        String code = "validCityCode";
        City city = new City();
        CityDTO cityDTO = new CityDTO();

        when(cityRepository.findByCode(code)).thenReturn(city);
        when(cityMapper.toCityDTO(city)).thenReturn(cityDTO);

        CityDTO result = adminCityService.getCityByCode(code);

        assertNotNull(result);
        assertEquals(cityDTO, result);
        verify(cityRepository, times(1)).findByCode(code);
        verify(cityMapper, times(1)).toCityDTO(city);
    }

    @Test
    void getCityByCode_shouldThrowDataNotFoundException_whenCityDoesNotExist() {
        String code = "invalidCityCode";

        when(cityRepository.findByCode(code)).thenReturn(null);

        assertThrows(DataNotFoundException.class, () -> adminCityService.getCityByCode(code));
        verify(cityRepository, times(1)).findByCode(code);
        verify(cityMapper, never()).toCityDTO(any());
    }
} 
