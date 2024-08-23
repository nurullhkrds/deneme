import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class InstitutionOrderPymGroupMapperTest {

    final InstitutionOrderPymGroupMapper mapper = InstitutionOrderPymGroupMapper.INSTANCE;

    @Test
    void testToInstitutionOrderPymGroupDTO() {
        InstitutionOrderPymGroup institutionOrderPymGroup = new InstitutionOrderPymGroup();
        institutionOrderPymGroup.setId(1L);
        institutionOrderPymGroup.setName("Test Order Payment Group");

        InstitutionOrderPymGroupDTO dto = mapper.toInstitutionOrderPymGroupDTO(institutionOrderPymGroup);

        assertNotNull(dto);
        assertEquals(institutionOrderPymGroup.getId(), dto.getId());
        assertEquals(institutionOrderPymGroup.getName(), dto.getName());
    }

    @Test
    void testToInstitutionOrderPymGroup() {
        InstitutionOrderPymGroupDTO dto = new InstitutionOrderPymGroupDTO();
        dto.setId(1L);
        dto.setName("Test Order Payment Group");

        InstitutionOrderPymGroup entity = mapper.toInstitutionOrderPymGroup(dto);

        assertNotNull(entity);
        assertEquals(dto.getId(), entity.getId());
        assertEquals(dto.getName(), entity.getName());
    }

    @Test
    void testNullMapping() {
        assertNull(mapper.toInstitutionOrderPymGroupDTO(null));
        assertNull(mapper.toInstitutionOrderPymGroup(null));
    }
}
