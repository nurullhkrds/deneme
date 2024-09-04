import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.MockedStatic;
import org.mockito.Mockito;
import javax.validation.ConstraintValidatorContext;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

class ChannelCodeValidatorTest {

    private ChannelCodeValidator channelCodeValidator;
    private ConstraintValidatorContext context;

    @BeforeEach
    void setUp() {
        channelCodeValidator = new ChannelCodeValidator();
        context = mock(ConstraintValidatorContext.class);  // Mocking the ConstraintValidatorContext
    }

    @Test
    void testIsValid_WithValidChannelCode_ShouldReturnTrue() {
        String validChannelCode = "VALID_CODE";

        // Mocking static method EnumChannel.parseValue
        try (MockedStatic<EnumChannel> mockedEnumChannel = Mockito.mockStatic(EnumChannel.class)) {
            mockedEnumChannel.when(() -> EnumChannel.parseValue(validChannelCode)).thenReturn(new EnumChannel());

            boolean result = channelCodeValidator.isValid(validChannelCode, context);

            assertTrue(result);
        }
    }

    @Test
    void testIsValid_WithInvalidChannelCode_ShouldReturnFalse() {
        String invalidChannelCode = "INVALID_CODE";

        // Mocking static method EnumChannel.parseValue
        try (MockedStatic<EnumChannel> mockedEnumChannel = Mockito.mockStatic(EnumChannel.class)) {
            mockedEnumChannel.when(() -> EnumChannel.parseValue(invalidChannelCode)).thenReturn(null);

            boolean result = channelCodeValidator.isValid(invalidChannelCode, context);

            assertFalse(result);
        }
    }

    @Test
    void testIsValid_WithEmptyChannelCode_ShouldReturnFalse() {
        String emptyChannelCode = "";

        boolean result = channelCodeValidator.isValid(emptyChannelCode, context);

        assertFalse(result);
    }

    @Test
    void testIsValid_WithNullChannelCode_ShouldReturnFalse() {
        String nullChannelCode = null;

        boolean result = channelCodeValidator.isValid(nullChannelCode, context);

        assertFalse(result);
    }
}
