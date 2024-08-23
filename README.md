import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

public class InstitutionNetworkInfoMapperTest {

    private final InstitutionNetworkInfoMapper mapper = InstitutionNetworkInfoMapper.INSTANCE;

    @Test
    public void testToInstitutionNetworkInfoDTO() {
        // Test için bir InstitutionNetworkInfo nesnesi oluştur
        InstitutionNetworkInfo institutionNetworkInfo = new InstitutionNetworkInfo();
        institutionNetworkInfo.setId(1L);
        institutionNetworkInfo.setName("Test Network");

        // Mapper'ı kullanarak DTO'ya dönüştür
        InstitutionNetworkInfoDTO dto = mapper.toInstitutionNetworkInfoDTO(institutionNetworkInfo);

        // Dönüşümün doğru olduğunu doğrula
        assertNotNull(dto);
        assertEquals(institutionNetworkInfo.getId(), dto.getId());
        assertEquals(institutionNetworkInfo.getName(), dto.getName());
    }

    @Test
    public void testToInstitutionNetworkInfo() {
        // Test için bir InstitutionNetworkInfoDTO nesnesi oluştur
        InstitutionNetworkInfoDTO dto = new InstitutionNetworkInfoDTO();
        dto.setId(1L);
        dto.setName("Test Network");

        // Mapper'ı kullanarak entity'e dönüştür
        InstitutionNetworkInfo entity = mapper.toInstitutionNetworkInfo(dto);

        // Dönüşümün doğru olduğunu doğrula
        assertNotNull(entity);
        assertEquals(dto.getId(), entity.getId());
        assertEquals(dto.getName(), entity.getName());
    }

    @Test
    public void testNullMapping() {
        // Null değeri ile test
        assertNull(mapper.toInstitutionNetworkInfoDTO(null));
        assertNull(mapper.toInstitutionNetworkInfo(null));
    }
}
