import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import java.util.Date;

public class AccountingDataLookupServiceImplTest {

    @Mock
    private AccountingDataLookUpServiceClient accountingDataLookUpServiceClient;

    @InjectMocks
    private AccountingDataLookupServiceImpl accountingDataLookupService;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    public void testGetNextBusinessDate() {
        // Arrange
        Date firstDate = new Date();
        Integer workDayCount = 5;
        Date expectedDate = new Date(firstDate.getTime() + (5 * 24 * 60 * 60 * 1000L)); // Example expected date

        RequestGetGlobalNextBusinessDate request = new RequestGetGlobalNextBusinessDate();
        request.setFirstDate(firstDate);
        request.setWorkDayCount(workDayCount);
        request.setCountry(EnumCountry.TURKEY.getCode());

        ResponseGetGlobalNextBusinessDate response = new ResponseGetGlobalNextBusinessDate();
        response.setRealDate(expectedDate);

        when(accountingDataLookUpServiceClient.getGlobalNextBusinessDate(any(RequestGetGlobalNextBusinessDate.class))).thenReturn(response);

        // Act
        Date actualDate = accountingDataLookupService.getNextBusinessDate(firstDate, workDayCount);

        // Assert
        assertEquals(expectedDate, actualDate);
        verify(accountingDataLookUpServiceClient, times(1)).getGlobalNextBusinessDate(any(RequestGetGlobalNextBusinessDate.class));
    }
}
