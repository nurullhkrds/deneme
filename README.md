import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.mockito.stubbing.Answer;

import java.util.Arrays;
import java.util.List;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.Mockito.when;

class InstitutionBarcodeServiceImplTest {

    @Mock
    private InstitutionFeatureService institutionFeatureService;

    @InjectMocks
    private InstitutionBarcodeServiceImpl institutionBarcodeService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getSubscriberNoWithBarcode_whenBarcodeFlagTrue_returnsSubscriberNo() {
        // Arrange
        String institutionCode = "INST123";
        String productCode = "PROD456";
        String barcode = "1234567890";
        String barcodeParserRule = "(?<subscriberNo>\\d{8})";
        
        when(institutionFeatureService.getFeatureValue(EnumFeatureCode.BARCODE_PAYMENT_FOR_INSTITUTION, institutionCode, productCode))
                .thenReturn("1");
        when(institutionFeatureService.getFeatureValue(EnumFeatureCode.BARCODE_PARSER_RULE_FOR_INSTITUTION, institutionCode, productCode))
                .thenReturn(barcodeParserRule);

        GetSubscriberNoWithBarcodeRequest request = new GetSubscriberNoWithBarcodeRequest();
        request.setInstitutionCode(institutionCode);
        request.setProductCode(productCode);
        request.setBarcode(barcode);

        // Act
        GetSubscriberNoWithBarcodeResponse response = institutionBarcodeService.getSubscriberNoWithBarcode(request);

        // Assert
        assertEquals("12345678", response.getSubscriberNo());
        assertEquals("0", response.getReturnCode());
        assertEquals("Success", response.getReturnMessage());
    }

    @Test
    void getSubscriberNoWithBarcode_whenBarcodeFlagFalse_returnsEmptySubscriberNo() {
        // Arrange
        String institutionCode = "INST123";
        String productCode = "PROD456";
        String barcode = "1234567890";
        String barcodeParserRule = "(?<subscriberNo>\\d{8})";

        when(institutionFeatureService.getFeatureValue(EnumFeatureCode.BARCODE_PAYMENT_FOR_INSTITUTION, institutionCode, productCode))
                .thenReturn("0");

        GetSubscriberNoWithBarcodeRequest request = new GetSubscriberNoWithBarcodeRequest();
        request.setInstitutionCode(institutionCode);
        request.setProductCode(productCode);
        request.setBarcode(barcode);

        // Act
        GetSubscriberNoWithBarcodeResponse response = institutionBarcodeService.getSubscriberNoWithBarcode(request);

        // Assert
        assertEquals("", response.getSubscriberNo());
        assertEquals("0", response.getReturnCode());
        assertEquals("Success", response.getReturnMessage());
    }

    @Test
    void getNamedGroup_withValidRegex_returnsNamedGroups() {
        // Arrange
        String regex = "(?<subscriberNo>\\d{8})(?<checkDigit>\\d)";

        // Act
        List<String> namedGroups = institutionBarcodeService.getNamedGroup(regex);

        // Assert
        assertEquals(Arrays.asList("checkDigit", "subscriberNo"), namedGroups);
    }
}
