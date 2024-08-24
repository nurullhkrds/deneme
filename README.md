import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.MockitoAnnotations;

import java.lang.reflect.Field;
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

import static org.junit.jupiter.api.Assertions.assertTrue;

public class PymBaseDTOTest {

    private PymBaseDTO pymBaseDTO;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
        pymBaseDTO = new PymBaseDTO();
    }

    @Test
    public void testToString_withPrimitiveFields() throws Exception {
        // Set up the fields in pymBaseDTO
        setField(pymBaseDTO, "primitiveField", 123);

        String result = pymBaseDTO.toString();

        // Check that the toString contains the primitive field value
        assertTrue(result.contains("primitiveField: 123"));
    }

    @Test
    public void testToString_withListField() throws Exception {
        // Set up the fields in pymBaseDTO
        List<String> list = Arrays.asList("item1", "item2");
        setField(pymBaseDTO, "listField", list);

        String result = pymBaseDTO.toString();

        // Check that the toString contains the list items
        assertTrue(result.contains("listField: item1"));
        assertTrue(result.contains("item2"));
    }

    @Test
    public void testToString_withNullField() throws Exception {
        // Set up the fields in pymBaseDTO
        setField(pymBaseDTO, "nullField", null);

        String result = pymBaseDTO.toString();

        // Check that the toString handles null values
        assertTrue(result.contains("nullField: null"));
    }

    @Test
    public void testToString_withNestedObject() throws Exception {
        // Set up a nested object
        PymBaseDTO nestedDTO = new PymBaseDTO();
        setField(pymBaseDTO, "nestedObject", nestedDTO);

        String result = pymBaseDTO.toString();

        // Check that the toString handles nested objects
        assertTrue(result.contains("nestedObject"));
    }

    // Utility method to set a private field using reflection
    private void setField(Object targetObject, String fieldName, Object value) throws Exception {
        Field field = targetObject.getClass().getDeclaredField(fieldName);
        field.setAccessible(true);
        field.set(targetObject, value);
    }
}
