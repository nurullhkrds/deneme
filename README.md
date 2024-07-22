import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

import org.apache.commons.lang3.StringUtils;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

public class BaseTransactionalEntityListenerTest {

    @InjectMocks
    private BaseTransactionalEntityListener baseTransactionalEntityListener;

    @Mock
    private RequestContext requestContext;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    public void testPrePersist_WhenAllFieldsAreEmpty_ShouldSetFieldsFromRequestContext() {
        BaseTransactionalEntity entity = new BaseTransactionalEntity();
        
        // Mock requestContext responses
        when(requestContext.getOperatingBranchCode()).thenReturn("branchCode");
        when(requestContext.getChannelCode()).thenReturn("channelCode");
        when(requestContext.getChannelSessionId()).thenReturn("channelSessionId");
        when(requestContext.getChannelTransactionId()).thenReturn("channelTransactionId");

        baseTransactionalEntityListener.prePersist(entity);

        assertEquals("branchCode", entity.getBranchCode());
        assertEquals("channelCode", entity.getChannelCode());
        assertEquals("channelSessionId", entity.getChannelSessionId());
        assertEquals("channelTransactionId", entity.getChannelTransactionId());
    }

    @Test
    public void testPrePersist_WhenFieldsAreAlreadySet_ShouldNotOverrideFields() {
        BaseTransactionalEntity entity = new BaseTransactionalEntity();
        entity.setBranchCode("existingBranchCode");
        entity.setChannelCode("existingChannelCode");
        entity.setChannelSessionId("existingChannelSessionId");
        entity.setChannelTransactionId("existingChannelTransactionId");
        
        // Mock requestContext responses
        when(requestContext.getOperatingBranchCode()).thenReturn("branchCode");
        when(requestContext.getChannelCode()).thenReturn("channelCode");
        when(requestContext.getChannelSessionId()).thenReturn("channelSessionId");
        when(requestContext.getChannelTransactionId()).thenReturn("channelTransactionId");

        baseTransactionalEntityListener.prePersist(entity);

        assertEquals("existingBranchCode", entity.getBranchCode());
        assertEquals("existingChannelCode", entity.getChannelCode());
        assertEquals("existingChannelSessionId", entity.getChannelSessionId());
        assertEquals("existingChannelTransactionId", entity.getChannelTransactionId());
    }

    @Test
    public void testPrePersist_WhenOnlySomeFieldsAreEmpty_ShouldSetOnlyEmptyFields() {
        BaseTransactionalEntity entity = new BaseTransactionalEntity();
        entity.setBranchCode("existingBranchCode");
        
        // Mock requestContext responses
        when(requestContext.getOperatingBranchCode()).thenReturn("branchCode");
        when(requestContext.getChannelCode()).thenReturn("channelCode");
        when(requestContext.getChannelSessionId()).thenReturn("channelSessionId");
        when(requestContext.getChannelTransactionId()).thenReturn("channelTransactionId");

        baseTransactionalEntityListener.prePersist(entity);

        assertEquals("existingBranchCode", entity.getBranchCode());
        assertEquals("channelCode", entity.getChannelCode());
        assertEquals("channelSessionId", entity.getChannelSessionId());
        assertEquals("channelTransactionId", entity.getChannelTransactionId());
    }
}
