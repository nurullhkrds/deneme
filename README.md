import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.fasterxml.jackson.datatype.jsr310.JavaTimeModule;
import org.junit.jupiter.api.Test;
import org.mockito.MockedStatic;
import org.mockito.Mockito;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import java.text.SimpleDateFormat;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

class JsonUtilTest {

    @Test
    void testConvertObjectToJsonString() throws JsonProcessingException {
        Object testObject = new TestObject("test", 123);
        
        // Mock ObjectMapper behavior
        ObjectMapper mapper = mock(ObjectMapper.class);
        when(mapper.writeValueAsString(any(Object.class))).thenReturn("{\"name\":\"test\",\"value\":123}");
        
        // Test the method
        try (MockedStatic<LoggerFactory> mockedLoggerFactory = Mockito.mockStatic(LoggerFactory.class);
             MockedStatic<ObjectMapper> mockedObjectMapper = Mockito.mockStatic(ObjectMapper.class)) {

            mockedObjectMapper.when(ObjectMapper::new).thenReturn(mapper);
            String json = JsonUtil.convertObjectToJsonString(testObject);

            // Verify
            assertNotNull(json);
            assertEquals("{\"name\":\"test\",\"value\":123}", json);
            verify(mapper).writeValueAsString(any(Object.class));
        }
    }

    @Test
    void testConvertStringToObject() throws JsonProcessingException {
        String rawJson = "{\"name\":\"test\",\"value\":123}";

        // Mock ObjectMapper behavior
        ObjectMapper objectMapper = mock(ObjectMapper.class);
        when(objectMapper.readValue(anyString(), eq(TestObject.class)))
                .thenReturn(new TestObject("test", 123));

        // Test the method
        try (MockedStatic<ObjectMapper> mockedObjectMapper = Mockito.mockStatic(ObjectMapper.class)) {
            mockedObjectMapper.when(ObjectMapper::new).thenReturn(objectMapper);
            TestObject obj = JsonUtil.convertStringToObject(rawJson, TestObject.class);

            // Verify
            assertNotNull(obj);
            assertEquals("test", obj.getName());
            assertEquals(123, obj.getValue());
            verify(objectMapper).readValue(anyString(), eq(TestObject.class));
        }
    }

    @Test
    void testConvertObjectToJsonStringWithoutException() {
        Object testObject = new TestObject("test", 123);

        // Mock ObjectMapper behavior
        ObjectMapper mapper = mock(ObjectMapper.class);
        try {
            when(mapper.writeValueAsString(any(Object.class))).thenReturn("{\"name\":\"test\",\"value\":123}");
        } catch (JsonProcessingException e) {
            fail("JsonProcessingException should not be thrown in this test.");
        }

        // Test the method
        try (MockedStatic<ObjectMapper> mockedObjectMapper = Mockito.mockStatic(ObjectMapper.class)) {
            mockedObjectMapper.when(ObjectMapper::new).thenReturn(mapper);
            String json = JsonUtil.convertObjectToJsonStringWithoutException(testObject);

            // Verify
            assertNotNull(json);
            assertEquals("{\"name\":\"test\",\"value\":123}", json);
            verify(mapper).writeValueAsString(any(Object.class));
        }
    }

    // TestObject class for testing purposes
    static class TestObject {
        private String name;
        private int value;

        public TestObject(String name, int value) {
            this.name = name;
            this.value = value;
        }

        public String getName() {
            return name;
        }

        public int getValue() {
            return value;
        }
    }
}
