import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;

class PaymentNotificationMapperTest {

    private final PaymentNotificationMapper mapper = PaymentNotificationMapper.INSTANCE;

    @Test
    void testToEntity() {
        PaymentNotificationDTO dto = new PaymentNotificationDTO();
        PaymentNotification entity = mapper.toEntity(dto);
        
        assertNotNull(entity);
    }

    @Test
    void testToDTO() {
        PaymentNotification entity = new PaymentNotification();
        PaymentNotificationDTO dto = mapper.toDTO(entity);
        
        assertNotNull(dto);
    }
}




import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;

class PaymentCancelMapperTest {

    private final PaymentCancelMapper mapper = PaymentCancelMapper.INSTANCE;

    @Test
    void testToEntity() {
        PaymentCancelDTO dto = new PaymentCancelDTO();
        PaymentCancel entity = mapper.toEntity(dto);
        
        assertNotNull(entity);
    }

    @Test
    void testToDTO() {
        PaymentCancel entity = new PaymentCancel();
        PaymentCancelDTO dto = mapper.toDTO(entity);
        
        assertNotNull(dto);
    }
}
