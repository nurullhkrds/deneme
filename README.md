import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

public class InstitutionChnnlPymMthdAccServiceImplTest {

    @Mock
    private InstitutionChnnlPymMthdAccRepository institutionChnnlPymMthdAccRepository;

    @Mock
    private InstitutionChnnlPymMthdAccMapper institutionChnnlPymMthdAccMapper;

    @InjectMocks
    private InstitutionChnnlPymMthdAccServiceImpl institutionChnnlPymMthdAccService;

    @BeforeEach
    public void setup() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    public void testGetInstitutionChnnlPymMthdAcc() {
        // Mock veriler
        Long institutionChannelPymMethodId = 1L;
        String currency = "USD";
        InstitutionChnnlPymMthdAcc institutionChnnlPymMthdAcc = new InstitutionChnnlPymMthdAcc(); // Mock nesne oluşturun
        InstitutionChnnlPymMthdAccDTO institutionChnnlPymMthdAccDTO = new InstitutionChnnlPymMthdAccDTO(); // Mock DTO oluşturun

        // Mock davranışlar
        when(institutionChnnlPymMthdAccRepository.findFirstByInstitutionChannelPymMethodAndCurrency(institutionChannelPymMethodId, currency))
                .thenReturn(institutionChnnlPymMthdAcc);
        when(institutionChnnlPymMthdAccMapper.toDTO(institutionChnnlPymMthdAcc))
                .thenReturn(institutionChnnlPymMthdAccDTO);

        // Servis çağrısı
        InstitutionChnnlPymMthdAccDTO result = institutionChnnlPymMthdAccService.getInstitutionChnnlPymMthdAcc(institutionChannelPymMethodId, currency);

        // Sonuç doğrulama
        assertNotNull(result);
        assertEquals(institutionChnnlPymMthdAccDTO, result);

        // Mock'ların doğru çağrıldığını doğrulama
        verify(institutionChnnlPymMthdAccRepository, times(1)).findFirstByInstitutionChannelPymMethodAndCurrency(institutionChannelPymMethodId, currency);
        verify(institutionChnnlPymMthdAccMapper, times(1)).toDTO(institutionChnnlPymMthdAcc);
    }
}
