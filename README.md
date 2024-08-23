import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

import java.util.List;
import java.util.ArrayList;

class ReceiptApiServiceImplTest {

    @Mock
    ReceiptClientService receiptClientService;

    @InjectMocks
    ReceiptApiServiceImpl receiptApiService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testPrintReceipt() {
        List<RequestApiReceiptDTO> request = new ArrayList<>();
        List<ResponseApiReceiptDTO> expectedResponse = new ArrayList<>();

        when(receiptClientService.printReceipt(request)).thenReturn(expectedResponse);

        List<ResponseApiReceiptDTO> actualResponse = receiptApiService.printReceipt(request);

        assertNotNull(actualResponse);
        assertEquals(expectedResponse, actualResponse);
        verify(receiptClientService, times(1)).printReceipt(request);
    }
}
