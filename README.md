import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;

class ChannelMapperTest {

    private final ChannelMapper mapper = ChannelMapper.INSTANCE;

    @Test
    void testToDto() {
        Channel entity = new Channel();
        ChannelDTO dto = mapper.toDto(entity);
        
        assertNotNull(dto);
    }

    @Test
    void testToEntity() {
        ChannelDTO dto = new ChannelDTO();
        Channel entity = mapper.toEntity(dto);
        
        assertNotNull(entity);
    }
}




import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;

class CityMapperTest {

    private final CityMapper mapper = CityMapper.INSTANCE;

    @Test
    void testToDto() {
        City entity = new City();
        CityDTO dto = mapper.toCityDTO(entity);
        
        assertNotNull(dto);
    }

    @Test
    void testToEntity() {
        CityDTO dto = new CityDTO();
        City entity = mapper.toCity(dto);
        
        assertNotNull(entity);
    }
}




import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;

class FeatureMapperTest {

    private final FeatureMapper mapper = FeatureMapper.INSTANCE;

    @Test
    void testToDto() {
        Feature entity = new Feature();
        FeatureDTO dto = mapper.toFeatureDTO(entity);
        
        assertNotNull(dto);
    }

    @Test
    void testToEntity() {
        FeatureDTO dto = new FeatureDTO();
        Feature entity = mapper.toFeature(dto);
        
        assertNotNull(entity);
    }
}




import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;

class InstitutionAccountingInfoMapperTest {

    private final InstitutionAccountingInfoMapper mapper = InstitutionAccountingInfoMapper.INSTANCE;

    @Test
    void testToDto() {
        InstitutionAccountingInfo entity = new InstitutionAccountingInfo();
        InstitutionAccountingInfoDTO dto = mapper.toInstitutionAccountingInfoDTO(entity);
        
        assertNotNull(dto);
    }

    @Test
    void testToEntity() {
        InstitutionAccountingInfoDTO dto = new InstitutionAccountingInfoDTO();
        InstitutionAccountingInfo entity = mapper.toInstitutionAccountingInfo(dto);
        
        assertNotNull(entity);
    }
}




import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;

class InstitutionChannelMapperTest {

    private final InstitutionChannelMapper mapper = InstitutionChannelMapper.INSTANCE;

    @Test
    void testToDto() {
        InstitutionChannel entity = new InstitutionChannel();
        InstitutionChannelDTO dto = mapper.toDTO(entity);
        
        assertNotNull(dto);
    }

    @Test
    void testToEntity() {
        InstitutionChannelDTO dto = new InstitutionChannelDTO();
        InstitutionChannel entity = mapper.toEntity(dto);
        
        assertNotNull(entity);
    }
}
