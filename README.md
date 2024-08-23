import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;

public class InstitutionAdkControllerTest {

    @Mock
    private ProductCityService productCityService;

    @Mock
    private InstitutionDetailService institutionDetailService;

    @Mock
    private RequestContext requestContext;

    @Mock
    private InstitutionService institutionService;

    @InjectMocks
    private InstitutionAdkController institutionAdkController;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testGetProductWithCityList() throws MicroException {
        // Arrange
        GetProductWithCityListRequest request = new GetProductWithCityListRequest();
        GetProductWithCityListResponse expectedResponse = new GetProductWithCityListResponse();
        when(productCityService.getProductWithCityList(any())).thenReturn(expectedResponse);

        // Act
        ResponseEntity<GetProductWithCityListResponse> response = institutionAdkController.getProductWithCityList(request, "channelTransactionId", "channelSessionId");

        // Assert
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(expectedResponse, response.getBody());
        verify(requestContext).setChannelSessionId("channelSessionId");
        verify(requestContext).setChannelTransactionId("channelTransactionId");
    }

    @Test
    void testGetInstitutionDetailList() throws MicroException {
        // Arrange
        GetInstitutionDetailListRequest request = new GetInstitutionDetailListRequest();
        GetInstitutionDetailListResponse expectedResponse = new GetInstitutionDetailListResponse();
        when(institutionDetailService.getInstitutionList(any())).thenReturn(expectedResponse);

        // Act
        ResponseEntity<GetInstitutionDetailListResponse> response = institutionAdkController.getInstitutionDetailList(request, "channelTransactionId", "channelSessionId");

        // Assert
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(expectedResponse, response.getBody());
        verify(requestContext).setChannelSessionId("channelSessionId");
        verify(requestContext).setChannelTransactionId("channelTransactionId");
    }

    @Test
    void testGetInstitutionProductList() throws MicroException {
        // Arrange
        GetProductWithInstitutionListResponse expectedResponse = new GetProductWithInstitutionListResponse();
        when(institutionService.getInstitutionAndProductCodeList()).thenReturn(expectedResponse);

        // Act
        ResponseEntity<GetProductWithInstitutionListResponse> response = institutionAdkController.getInstitutionDetailList();

        // Assert
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertEquals(expectedResponse, response.getBody());
    }
}
