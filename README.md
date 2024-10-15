import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;
import java.time.LocalTime;
import java.util.*;

public class LombokAnnotationsTest {

    // InstitutionUserIntfWebDTO Tests
    @Test
    public void testInstitutionUserIntfWebDTONoArgsConstructor() {
        InstitutionUserIntfWebDTO dto = new InstitutionUserIntfWebDTO();
        assertNotNull(dto);
    }

    @Test
    public void testInstitutionUserIntfWebDTOAllArgsConstructor() {
        Set<InstUserIntfSubtypeWebDTO> multiOptionList = new HashSet<>();
        multiOptionList.add(new InstUserIntfSubtypeWebDTO("key1", "value1"));
        
        InstitutionUserIntfWebDTO dto = new InstitutionUserIntfWebDTO(
                1L, "Label", "Label Desc", true, 10, 1, true, 
                1, EnumInterfaceType.SOME_TYPE, "Explanation", multiOptionList
        );

        assertNotNull(dto);
        assertEquals(1L, dto.getId());
        assertEquals("Label", dto.getLabel());
        assertTrue(dto.getIsNumeric());
    }

    @Test
    public void testInstitutionUserIntfWebDTOEqualsAndHashCode() {
        Set<InstUserIntfSubtypeWebDTO> multiOptionList1 = new HashSet<>();
        multiOptionList1.add(new InstUserIntfSubtypeWebDTO("key1", "value1"));
        
        InstitutionUserIntfWebDTO dto1 = new InstitutionUserIntfWebDTO(
                1L, "Label", "Label Desc", true, 10, 1, true, 
                1, EnumInterfaceType.SOME_TYPE, "Explanation", multiOptionList1
        );

        InstitutionUserIntfWebDTO dto2 = new InstitutionUserIntfWebDTO(
                1L, "Label", "Label Desc", true, 10, 1, true, 
                1, EnumInterfaceType.SOME_TYPE, "Explanation", multiOptionList1
        );

        assertEquals(dto1, dto2);
        assertEquals(dto1.hashCode(), dto2.hashCode());
    }

    // InstitutionDebtTypeWebDTO Tests
    @Test
    public void testInstitutionDebtTypeWebDTONoArgsConstructor() {
        InstitutionDebtTypeWebDTO dto = new InstitutionDebtTypeWebDTO();
        assertNotNull(dto);
    }

    @Test
    public void testInstitutionDebtTypeWebDTOAllArgsConstructor() {
        Set<InstitutionUserIntfWebDTO> institutionUserIntfList = new HashSet<>();
        List<String> paymentMethodList = Arrays.asList("Method1", "Method2");
        List<String> accountingCurrencyList = Arrays.asList("USD", "EUR");

        InstitutionDebtTypeWebDTO dto = new InstitutionDebtTypeWebDTO(
                1L, "DebtType", "Explanation", institutionUserIntfList, 
                true, true, LocalTime.of(9, 0), LocalTime.of(17, 0), 
                true, paymentMethodList, accountingCurrencyList
        );

        assertNotNull(dto);
        assertEquals(1L, dto.getId());
        assertEquals("DebtType", dto.getDebtType());
        assertTrue(dto.getIsPartialPaymentAllowed());
    }

    @Test
    public void testInstitutionDebtTypeWebDTOEqualsAndHashCode() {
        Set<InstitutionUserIntfWebDTO> institutionUserIntfList = new HashSet<>();
        List<String> paymentMethodList = Arrays.asList("Method1", "Method2");
        List<String> accountingCurrencyList = Arrays.asList("USD", "EUR");

        InstitutionDebtTypeWebDTO dto1 = new InstitutionDebtTypeWebDTO(
                1L, "DebtType", "Explanation", institutionUserIntfList, 
                true, true, LocalTime.of(9, 0), LocalTime.of(17, 0), 
                true, paymentMethodList, accountingCurrencyList
        );

        InstitutionDebtTypeWebDTO dto2 = new InstitutionDebtTypeWebDTO(
                1L, "DebtType", "Explanation", institutionUserIntfList, 
                true, true, LocalTime.of(9, 0), LocalTime.of(17, 0), 
                true, paymentMethodList, accountingCurrencyList
        );

        assertEquals(dto1, dto2);
        assertEquals(dto1.hashCode(), dto2.hashCode());
    }

    // InstUserIntfSubtypeWebDTO Tests
    @Test
    public void testInstUserIntfSubtypeWebDTONoArgsConstructor() {
        InstUserIntfSubtypeWebDTO dto = new InstUserIntfSubtypeWebDTO();
        assertNotNull(dto);
    }

    @Test
    public void testInstUserIntfSubtypeWebDTOAllArgsConstructor() {
        InstUserIntfSubtypeWebDTO dto = new InstUserIntfSubtypeWebDTO("Key123", "Value123");

        assertNotNull(dto);
        assertEquals("Key123", dto.getKey());
        assertEquals("Value123", dto.getValue());
    }

    @Test
    public void testInstUserIntfSubtypeWebDTOEqualsAndHashCode() {
        InstUserIntfSubtypeWebDTO dto1 = new InstUserIntfSubtypeWebDTO("Key123", "Value123");
        InstUserIntfSubtypeWebDTO dto2 = new InstUserIntfSubtypeWebDTO("Key123", "Value123");

        assertEquals(dto1, dto2);
        assertEquals(dto1.hashCode(), dto2.hashCode());
    }

    // CityWebDTO Tests
    @Test
    public void testCityWebDTONoArgsConstructor() {
        CityWebDTO dto = new CityWebDTO();
        assertNotNull(dto);
    }

    @Test
    public void testCityWebDTOAllArgsConstructor() {
        CityWebDTO dto = new CityWebDTO("C123", "CityName");

        assertNotNull(dto);
        assertEquals("C123", dto.getCode());
        assertEquals("CityName", dto.getName());
    }

    @Test
    public void testCityWebDTOEqualsAndHashCode() {
        CityWebDTO dto1 = new CityWebDTO("C123", "CityName");
        CityWebDTO dto2 = new CityWebDTO("C123", "CityName");

        assertEquals(dto1, dto2);
        assertEquals(dto1.hashCode(), dto2.hashCode());
    }

    @Test
    public void testCityWebDTOCompareTo() {
        CityWebDTO dto1 = new CityWebDTO("C123", "CityName");
        CityWebDTO dto2 = new CityWebDTO("C124", "AnotherCity");

        assertTrue(dto1.compareTo(dto2) < 0);  // C123 is lexicographically smaller than C124
    }
}
