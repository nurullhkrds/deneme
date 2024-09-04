import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import java.time.LocalDateTime;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.ArgumentMatchers.anyString;
import static org.mockito.Mockito.*;

class UpdatableBaseEntityListenerTest {

    @InjectMocks
    private UpdatableBaseEntityListener updatableBaseEntityListener;

    @Mock
    private RequestContext requestContext;

    private UpdatableBaseEntity updatableBaseEntity;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
        updatableBaseEntity = mock(UpdatableBaseEntity.class);
    }

    @Test
    void testPreUpdate_setsUpdateDate() {
        // Arrange
        LocalDateTime now = LocalDateTime.now();

        // Act
        updatableBaseEntityListener.preUpdate(updatableBaseEntity);

        // Assert
        verify(updatableBaseEntity, times(1)).setUpdateDate(any(LocalDateTime.class));
        assertNotNull(updatableBaseEntity.getUpdateDate());
        assertTrue(updatableBaseEntity.getUpdateDate().isAfter(now) || updatableBaseEntity.getUpdateDate().equals(now));
    }

    @Test
    void testPreUpdate_whenUpdatedByIsEmptyAndAgentCodeExists() {
        // Arrange
        when(updatableBaseEntity.getUpdatedBy()).thenReturn("");
        when(requestContext.getAgentCode()).thenReturn("AGENT123");

        // Act
        updatableBaseEntityListener.preUpdate(updatableBaseEntity);

        // Assert
        verify(updatableBaseEntity, times(1)).setUpdatedBy("AGENT123");
        assertEquals("AGENT123", updatableBaseEntity.getUpdatedBy());
    }

    @Test
    void testPreUpdate_whenUpdatedByIsEmptyAndAgentCodeIsEmpty() {
        // Arrange
        when(updatableBaseEntity.getUpdatedBy()).thenReturn("");
        when(requestContext.getAgentCode()).thenReturn("");

        // Act
        updatableBaseEntityListener.preUpdate(updatableBaseEntity);

        // Assert
        verify(updatableBaseEntity, times(1)).setUpdatedBy("SYSTEM");
        assertEquals("SYSTEM", updatableBaseEntity.getUpdatedBy());
    }

    @Test
    void testPreUpdate_whenUpdatedByIsNotEmpty() {
        // Arrange
        when(updatableBaseEntity.getUpdatedBy()).thenReturn("EXISTING_USER");

        // Act
        updatableBaseEntityListener.preUpdate(updatableBaseEntity);

        // Assert
        verify(updatableBaseEntity, never()).setUpdatedBy(anyString());
        assertEquals("EXISTING_USER", updatableBaseEntity.getUpdatedBy());
    }
}
