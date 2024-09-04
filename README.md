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

class CreatableBaseEntityListenerTest {

    @InjectMocks
    private CreatableBaseEntityListener creatableBaseEntityListener;

    @Mock
    private RequestContext requestContext;

    private CreatableBaseEntity creatableBaseEntity;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
        creatableBaseEntity = mock(CreatableBaseEntity.class);
    }

    @Test
    void testPrePersist_whenCreateDateIsNull() {
        // Arrange
        when(creatableBaseEntity.getCreateDate()).thenReturn(null);
        LocalDateTime now = LocalDateTime.now();

        // Act
        creatableBaseEntityListener.prePersist(creatableBaseEntity);

        // Assert
        verify(creatableBaseEntity, times(1)).setCreateDate(any(LocalDateTime.class));
        // assert with the expected logic to ensure the value set is recent.
        assertNotNull(creatableBaseEntity.getCreateDate());
        assertTrue(creatableBaseEntity.getCreateDate().isAfter(now) || creatableBaseEntity.getCreateDate().equals(now));
    }

    @Test
    void testPrePersist_whenCreateDateIsNotNull() {
        // Arrange
        LocalDateTime existingDate = LocalDateTime.of(2023, 1, 1, 12, 0);
        when(creatableBaseEntity.getCreateDate()).thenReturn(existingDate);

        // Act
        creatableBaseEntityListener.prePersist(creatableBaseEntity);

        // Assert
        verify(creatableBaseEntity, never()).setCreateDate(any(LocalDateTime.class));
        assertEquals(existingDate, creatableBaseEntity.getCreateDate());
    }

    @Test
    void testPrePersist_whenCreatedByIsEmptyAndAgentCodeExists() {
        // Arrange
        when(creatableBaseEntity.getCreatedBy()).thenReturn("");
        when(requestContext.getAgentCode()).thenReturn("AGENT123");

        // Act
        creatableBaseEntityListener.prePersist(creatableBaseEntity);

        // Assert
        verify(creatableBaseEntity, times(1)).setCreatedBy("AGENT123");
        assertEquals("AGENT123", creatableBaseEntity.getCreatedBy());
    }

    @Test
    void testPrePersist_whenCreatedByIsEmptyAndAgentCodeIsEmpty() {
        // Arrange
        when(creatableBaseEntity.getCreatedBy()).thenReturn("");
        when(requestContext.getAgentCode()).thenReturn("");

        // Act
        creatableBaseEntityListener.prePersist(creatableBaseEntity);

        // Assert
        verify(creatableBaseEntity, times(1)).setCreatedBy("SYSTEM");
        assertEquals("SYSTEM", creatableBaseEntity.getCreatedBy());
    }

    @Test
    void testPrePersist_whenCreatedByIsNotEmpty() {
        // Arrange
        when(creatableBaseEntity.getCreatedBy()).thenReturn("EXISTING_USER");

        // Act
        creatableBaseEntityListener.prePersist(creatableBaseEntity);

        // Assert
        verify(creatableBaseEntity, never()).setCreatedBy(anyString());
        assertEquals("EXISTING_USER", creatableBaseEntity.getCreatedBy());
    }
}
