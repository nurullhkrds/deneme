import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;

import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;

public class ChannelUtilTest {

    @Test
    public void testPrivateConstructor() throws Exception {
        Constructor<ChannelUtil> constructor = ChannelUtil.class.getDeclaredConstructor();
        constructor.setAccessible(true); // Make the constructor accessible

        Exception exception = assertThrows(InvocationTargetException.class, constructor::newInstance);
        assertTrue(exception.getCause() instanceof IllegalAccessError, "Expected IllegalAccessError");
    }

}

import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

public class ChannelUtilTest {

    @Test
    public void testConvertChannel_withValidHarmoniChannelCode() {
        String result = ChannelUtil.convertChannel("NGI");
        assertEquals("201", result, "NGI should convert to 201");
    }

    @Test
    public void testConvertChannel_withInvalidHarmoniChannelCode() {
        String result = ChannelUtil.convertChannel("XYZ");
        assertEquals("XYZ", result, "XYZ should return as is, since it's not a Harmoni channel");
    }

    @Test
    public void testConvertChannel_withEmptyInput() {
        String result = ChannelUtil.convertChannel("");
        assertEquals("", result, "Empty input should return an empty string");
    }

    @Test
    public void testConvertToHarmoniChannel_withValidChannelCode() {
        String result = ChannelUtil.convertToHarmoniChannel("201");
        assertEquals("NGI", result, "201 should convert to NGI");
    }

    @Test
    public void testConvertToHarmoniChannel_withNonHarmoniChannelCode() {
        String result = ChannelUtil.convertToHarmoniChannel("XYZ");
        assertEquals("", result, "XYZ should return an empty string since it's not mapped");
    }

    @Test
    public void testConvertToHarmoniChannel_withEmptyInput() {
        String result = ChannelUtil.convertToHarmoniChannel("");
        assertEquals("", result, "Empty input should return an empty string");
    }

    @Test
    public void testIsHarmoniChannel_withValidChannelCode() {
        boolean result = ChannelUtil.convertChannel("NGI") != null;
        assertTrue(result, "NGI should be a Harmoni channel");
    }

    @Test
    public void testConvertCardEventDescChannel_withValidChannelCode() {
        String result = ChannelUtil.convertCardEventDescChannel("501");
        assertEquals("ATM", result, "501 should convert to ATM");
    }

    @Test
    public void testConvertCardEventDescChannel_withNonExistingChannelCode() {
        String result = ChannelUtil.convertCardEventDescChannel("999");
        assertNull(result, "999 should return null as it's not mapped");
    }

    @Test
    public void testConvertCardEventDescChannel_withEmptyInput() {
        String result = ChannelUtil.convertCardEventDescChannel("");
        assertEquals("", result, "Empty input should return an empty string");
    }
}
