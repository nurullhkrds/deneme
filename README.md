import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import java.util.List;
import java.util.Map;

@ExtendWith(MockitoExtension.class)
public class ProvisionFactoryTest {

    @Mock
    private ProvisionService provisionService1;

    @Mock
    private ProvisionService provisionService2;

    @InjectMocks
    private ProvisionFactory provisionFactory;

    @BeforeEach
    void setUp() {
        when(provisionService1.getProvisionType()).thenReturn(EnumProvisionType.TYPE1);
        when(provisionService2.getProvisionType()).thenReturn(EnumProvisionType.TYPE2);
    }

    @Test
    void whenProvisionFactoryIsInitialized_thenProvisionServiceMapIsCorrectlyPopulated() {
        List<ProvisionService> provisionServices = List.of(provisionService1, provisionService2);
        provisionFactory = new ProvisionFactory(provisionServices);

        Map<EnumProvisionType, ProvisionService> provisionServiceMap = ProvisionFactory.getProvisionServiceMap();
        
        assertEquals(2, provisionServiceMap.size());
        assertEquals(provisionService1, provisionServiceMap.get(EnumProvisionType.TYPE1));
        assertEquals(provisionService2, provisionServiceMap.get(EnumProvisionType.TYPE2));
    }
}
