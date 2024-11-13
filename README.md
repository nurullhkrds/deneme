import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import com.ykb.architecture.micro.error.exception.DataNotFoundException;
import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.common.exception.BillExceptionsUI;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminFeatureMapper;
import com.ykb.payments.bill.transaction.institution.domain.Feature;
import com.ykb.payments.bill.transaction.institution.dto.FeatureDTO;
import com.ykb.payments.bill.transaction.institution.enums.EnumFeatureCode;
import com.ykb.payments.bill.transaction.institution.repository.FeatureRepository;

class AdminFeatureServiceImplTest {

    @Mock
    private FeatureRepository featureRepository;

    @Mock
    private AdminFeatureMapper featureMapper;

    @InjectMocks
    private AdminFeatureServiceImpl adminFeatureService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getFeatureByCode_shouldReturnFeatureDTO_whenFeatureExists() throws MicroException {
        EnumFeatureCode code = EnumFeatureCode.SOME_VALID_CODE;
        Feature feature = new Feature();
        FeatureDTO featureDTO = new FeatureDTO();

        when(featureRepository.findByCode(code)).thenReturn(feature);
        when(featureMapper.toFeatureDTO(feature)).thenReturn(featureDTO);

        FeatureDTO result = adminFeatureService.getFeatureByCode(code);

        assertNotNull(result);
        assertEquals(featureDTO, result);
        verify(featureRepository, times(1)).findByCode(code);
        verify(featureMapper, times(1)).toFeatureDTO(feature);
    }

    @Test
    void getFeatureByCode_shouldThrowDataNotFoundException_whenFeatureDoesNotExist() {
        EnumFeatureCode code = EnumFeatureCode.SOME_INVALID_CODE;

        when(featureRepository.findByCode(code)).thenReturn(null);

        assertThrows(DataNotFoundException.class, () -> adminFeatureService.getFeatureByCode(code));
        verify(featureRepository, times(1)).findByCode(code);
        verify(featureMapper, never()).toFeatureDTO(any());
    }
} 
