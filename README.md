import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

public class ProvisionNextServiceImplTest {

    @Mock
    private ProvisionNextClient provisionNextClient;

    @InjectMocks
    private ProvisionNextServiceImpl provisionNextService;

    @BeforeEach
    public void setup() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    public void testMakeProvision() {
        // Mock veriler
        MakeProvisionRequest request = new MakeProvisionRequest(); // Test için uygun bir istek nesnesi oluşturun
        MakeProvisionResponse expectedResponse = new MakeProvisionResponse(); // Test için uygun bir yanıt nesnesi oluşturun

        // Mock davranışlar
        when(provisionNextClient.makeProvision(request)).thenReturn(expectedResponse);

        // Servis çağrısı
        MakeProvisionResponse actualResponse = provisionNextService.makeProvision(request);

        // Sonuç doğrulama
        assertNotNull(actualResponse);
        assertEquals(expectedResponse, actualResponse);

        // Mock'un doğru çağrıldığını doğrulama
        verify(provisionNextClient, times(1)).makeProvision(request);
    }

    @Test
    public void testMakeReverseProvision() {
        // Mock veriler
        MakeReverseProvisionRequest request = new MakeReverseProvisionRequest(); // Test için uygun bir istek nesnesi oluşturun
        MakeReverseProvisionResponse expectedResponse = new MakeReverseProvisionResponse(); // Test için uygun bir yanıt nesnesi oluşturun

        // Mock davranışlar
        when(provisionNextClient.makeReverseProvision(request)).thenReturn(expectedResponse);

        // Servis çağrısı
        MakeReverseProvisionResponse actualResponse = provisionNextService.makeReverseProvision(request);

        // Sonuç doğrulama
        assertNotNull(actualResponse);
        assertEquals(expectedResponse, actualResponse);

        // Mock'un doğru çağrıldığını doğrulama
        verify(provisionNextClient, times(1)).makeReverseProvision(request);
    }
}
