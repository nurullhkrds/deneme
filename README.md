import static org.junit.jupiter.api.Assertions.*;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.junit.jupiter.api.Test;

public class EnumReturnTypeTest {

    private static final ObjectMapper objectMapper = new ObjectMapper();

    @Test
    public void testEnumValues() {
        assertEquals("SUCCESS", EnumReturnType.SUCCESS.getValue());
        assertEquals("ERROR", EnumReturnType.ERROR.getValue());
    }

    @Test
    public void testFromJsonValue() {
        assertEquals(EnumReturnType.SUCCESS, EnumReturnType.getReturnType("SUCCESS"));
        assertEquals(EnumReturnType.ERROR, EnumReturnType.getReturnType("ERROR"));
        assertNull(EnumReturnType.getReturnType("UNKNOWN")); // Beklenmeyen bir değer olduğunda null dönmeli
    }

    @Test
    public void testToJsonValue() throws JsonProcessingException {
        String successJson = objectMapper.writeValueAsString(EnumReturnType.SUCCESS);
        String errorJson = objectMapper.writeValueAsString(EnumReturnType.ERROR);

        assertEquals("\"SUCCESS\"", successJson);
        assertEquals("\"ERROR\"", errorJson);
    }

    @Test
    public void testFromJsonCreator() throws JsonProcessingException {
        EnumReturnType successEnum = objectMapper.readValue("\"SUCCESS\"", EnumReturnType.class);
        EnumReturnType errorEnum = objectMapper.readValue("\"ERROR\"", EnumReturnType.class);

        assertEquals(EnumReturnType.SUCCESS, successEnum);
        assertEquals(EnumReturnType.ERROR, errorEnum);
    }
}
