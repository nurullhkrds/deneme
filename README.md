import static org.junit.jupiter.api.Assertions.assertTrue;
import static org.junit.jupiter.api.Assertions.assertFalse;
import static org.mockito.Mockito.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

public class PaymentUtilImplTest {

    @Mock
    private InstitutionFeatureService institutionFeatureService;

    @InjectMocks
    private PaymentUtilImpl paymentUtilImpl;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    public void testIsFomOperationEnabled_WhenFomCheckFlagIsTrue() {
        InstitutionDTO institution = new InstitutionDTO();
        institution.setProduct(new ProductDTO());
        institution.getProduct().setFomCheckFlag(true);

        boolean result = paymentUtilImpl.isFomOperationEnabled(institution);

        assertTrue(result, "Fom operation should be enabled when FomCheckFlag is true");
    }

    @Test
    public void testIsFomOperationEnabled_WhenFeatureValueIsYes() {
        InstitutionDTO institution = new InstitutionDTO();
        institution.setProduct(new ProductDTO());
        institution.getProduct().setFomCheckFlag(false);

        when(institutionFeatureService.getFeatureValue(EnumFeatureCode.FOM_CHECK_ENABLED, institution, institution.getProduct()))
            .thenReturn(EnumYesNo.YES.getValue());

        boolean result = paymentUtilImpl.isFomOperationEnabled(institution);

        assertTrue(result, "Fom operation should be enabled when feature value is 'YES'");
    }

    @Test
    public void testIsFomOperationEnabled_WhenFeatureValueIsNo() {
        InstitutionDTO institution = new InstitutionDTO();
        institution.setProduct(new ProductDTO());
        institution.getProduct().setFomCheckFlag(false);

        when(institutionFeatureService.getFeatureValue(EnumFeatureCode.FOM_CHECK_ENABLED, institution, institution.getProduct()))
            .thenReturn(EnumYesNo.NO.getValue());

        boolean result = paymentUtilImpl.isFomOperationEnabled(institution);

        assertFalse(result, "Fom operation should not be enabled when feature value is 'NO'");
    }

    @Test
    public void testIsFomOperationEnabled_WhenFeatureValueIsNull() {
        InstitutionDTO institution = new InstitutionDTO();
        institution.setProduct(new ProductDTO());
        institution.getProduct().setFomCheckFlag(false);

        when(institutionFeatureService.getFeatureValue(EnumFeatureCode.FOM_CHECK_ENABLED, institution, institution.getProduct()))
            .thenReturn(null);

        boolean result = paymentUtilImpl.isFomOperationEnabled(institution);

        assertFalse(result, "Fom operation should not be enabled when feature value is null");
    }
}
