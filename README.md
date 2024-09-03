import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

public class EnumServiceDirectionTypeTest {

    @Test
    void testEnumValues() {
        assertEquals("ADK-TO-INTERNAL", EnumServiceDirectionType.ADK_TO_INTERNAL.getValue());
        assertEquals("EXTERNAL-TO-INTERNAL", EnumServiceDirectionType.EXTERNAL_TO_INTERNAL.getValue());
        assertEquals("INTERNAL-TO-EXTERNAL", EnumServiceDirectionType.INTERNAL_TO_EXTERNAL.getValue());
        assertEquals("INTERNAL-TO-INTERNAL", EnumServiceDirectionType.INTERNAL_TO_INTERNAL.getValue());
    }

    @Test
    void testEnumFromString() {
        assertEquals(EnumServiceDirectionType.ADK_TO_INTERNAL, EnumServiceDirectionType.valueOf("ADK_TO_INTERNAL"));
        assertEquals(EnumServiceDirectionType.EXTERNAL_TO_INTERNAL, EnumServiceDirectionType.valueOf("EXTERNAL_TO_INTERNAL"));
        assertEquals(EnumServiceDirectionType.INTERNAL_TO_EXTERNAL, EnumServiceDirectionType.valueOf("INTERNAL_TO_EXTERNAL"));
        assertEquals(EnumServiceDirectionType.INTERNAL_TO_INTERNAL, EnumServiceDirectionType.valueOf("INTERNAL_TO_INTERNAL"));
    }
}
