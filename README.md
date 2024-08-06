import static org.mockito.Mockito.when;
import static org.junit.jupiter.api.Assertions.*;

import java.lang.reflect.Method;

import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

public class CardProvisionServiceImplTest {

    @Mock
    private SwtSwitchIntegrationService cardProvisionService;

    @Mock
    private ProvisionNextService provisionNextService;

    @Mock
    private AccountingUtilServiceImpl accountingUtilServiceImpl;

    @Mock
    private AccountingUtil accountingDateUtil;

    @InjectMocks
    private CardProvisionServiceImpl cardProvisionServiceImpl;

    @Mock
    private InstitutionChnnlPymMthdPscDTO pscDTO;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.initMocks(this);
    }

    @Test
    void testGetBlockDayCount() throws Exception {
        InstitutionChannelPymMethodDTO methodDTO = new InstitutionChannelPymMethodDTO();
        methodDTO.setBlockDayStrategyCode(EnumBlockDayStrategyCode.DAILY);

        // Mock the pscDTO to return a valid block day count
        when(pscDTO.getBlockDayCount(anyInt())).thenReturn(3);

        // Access the private method using reflection
        Method method = CardProvisionServiceImpl.class.getDeclaredMethod("getBlockDayCount", InstitutionChannelPymMethodDTO.class, InstitutionChnnlPymMthdPscDTO.class);
        method.setAccessible(true);
        int blockDayCount = (int) method.invoke(cardProvisionServiceImpl, methodDTO, pscDTO);

        assertEquals(3, blockDayCount);
    }
}
