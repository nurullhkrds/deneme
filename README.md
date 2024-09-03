import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

public class BusinessLogMapperTest {

    private final BusinessLogMapper mapper = BusinessLogMapper.INSTANCE;

    @Test
    void testToEntity() {
        BusinessLogDTO dto = new BusinessLogDTO();
        // DTO nesnesinin gerekli alanlarını doldurun
        dto.setId(1L);
        dto.setLogData("Test Log Data");

        BusinessLog entity = mapper.toEntity(dto);

        assertNotNull(entity);
        assertEquals(dto.getId(), entity.getId());
        assertEquals(dto.getLogData(), entity.getLogData());
    }
}



import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

public class ProcessLogMapperTest {

    private final ProcessLogMapper mapper = ProcessLogMapper.INSTANCE;

    @Test
    void testToEntity() {
        ProcessLogDTO dto = new ProcessLogDTO();
        // DTO nesnesinin gerekli alanlarını doldurun
        dto.setId(1L);
        dto.setProcessData("Test Process Data");

        ProcessLog entity = mapper.toEntity(dto);

        assertNotNull(entity);
        assertEquals(dto.getId(), entity.getId());
        assertEquals(dto.getProcessData(), entity.getProcessData());
    }
}




import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

public class ServiceLogMapperTest {

    private final ServiceLogMapper mapper = ServiceLogMapper.INSTANCE;

    @Test
    void testToEntity() {
        ServiceLogDTO dto = new ServiceLogDTO();
        // DTO nesnesinin gerekli alanlarını doldurun
        dto.setId(1L);
        dto.setServiceData("Test Service Data");

        ServiceLog entity = mapper.toEntity(dto);

        assertNotNull(entity);
        assertEquals(dto.getId(), entity.getId());
        assertEquals(dto.getServiceData(), entity.getServiceData());
    }
}
