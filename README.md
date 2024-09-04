import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;
import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import java.text.SimpleDateFormat;

public class JsonUtilTest {

    @Test
    public void testConvertObjectToJsonString() throws JsonProcessingException {
        // Create a sample object
        SampleObject sample = new SampleObject("John", 30);
        
        // Convert the object to JSON string
        String jsonString = JsonUtil.convertObjectToJsonString(sample);

        // Assert that the JSON string is correctly formed
        assertTrue(jsonString.contains("\"name\":\"John\""));
        assertTrue(jsonString.contains("\"age\":30"));
    }

    @Test
    public void testConvertStringToObject() throws JsonProcessingException {
        // Create a sample JSON string
        String jsonString = "{\"name\":\"John\",\"age\":30}";

        // Convert the JSON string to a SampleObject
        SampleObject sample = JsonUtil.convertStringToObject(jsonString, SampleObject.class);

        // Assert that the object is correctly formed
        assertEquals("John", sample.getName());
        assertEquals(30, sample.getAge());
    }

    @Test
    public void testConvertObjectToJsonStringWithoutException_validObject() {
        // Create a sample object
        SampleObject sample = new SampleObject("John", 30);
        
        // Convert the object to JSON string without throwing exception
        String jsonString = JsonUtil.convertObjectToJsonStringWithoutException(sample);

        // Assert that the JSON string is correctly formed
        assertTrue(jsonString.contains("\"name\":\"John\""));
        assertTrue(jsonString.contains("\"age\":30"));
    }

    @Test
    public void testConvertObjectToJsonStringWithoutException_nullObject() {
        // Test with a null object, expecting an empty string
        String jsonString = JsonUtil.convertObjectToJsonStringWithoutException(null);
        assertEquals("", jsonString, "Null object should return an empty string");
    }

    @Test
    public void testConvertObjectToJsonString_throwsException() {
        // Test with an object that cannot be serialized, e.g., a circular reference
        assertThrows(JsonProcessingException.class, () -> {
            Object objectWithCircularReference = new ObjectMapper(); // ObjectMapper cannot serialize itself
            JsonUtil.convertObjectToJsonString(objectWithCircularReference);
        });
    }

    // Sample object for testing
    public static class SampleObject {
        private String name;
        private int age;

        public SampleObject() {}

        public SampleObject(String name, int age) {
            this.name = name;
            this.age = age;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public int getAge() {
            return age;
        }

        public void setAge(int age) {
            this.age = age;
        }
    }
}
