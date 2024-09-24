import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;

public class MerchantInformationMapperTest {

    private final MerchantInformationMapper merchantInformationMapper = MerchantInformationMapper.INSTANCE;

    @Test
    void shouldMapMerchantInformationToDTO() {
        MerchantInformation merchantInformation = new MerchantInformation();
        merchantInformation.setId(1L);
        merchantInformation.setName("Test Merchant");

        MerchantInformationDTO merchantInformationDTO = merchantInformationMapper.toMerchantInformationDTO(merchantInformation);

        assertNotNull(merchantInformationDTO);
        assertEquals(merchantInformation.getId(), merchantInformationDTO.getId());
        assertEquals(merchantInformation.getName(), merchantInformationDTO.getName());
    }

    @Test
    void shouldMapDTOToMerchantInformation() {
        MerchantInformationDTO merchantInformationDTO = new MerchantInformationDTO();
        merchantInformationDTO.setId(1L);
        merchantInformationDTO.setName("Test Merchant DTO");

        MerchantInformation merchantInformation = merchantInformationMapper.toMerchantInformation(merchantInformationDTO);

        assertNotNull(merchantInformation);
        assertEquals(merchantInformationDTO.getId(), merchantInformation.getId());
        assertEquals(merchantInformationDTO.getName(), merchantInformation.getName());
    }
}

----------------------


import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;

public class InstUserIntfSubtypeMapperTest {

    private final InstUserIntfSubtypeMapper instUserIntfSubtypeMapper = InstUserIntfSubtypeMapper.INSTANCE;

    @Test
    void shouldMapInstUserIntfSubtypeToDTO() {
        InstUserIntfSubtype instUserIntfSubtype = new InstUserIntfSubtype();
        instUserIntfSubtype.setId(1L);
        instUserIntfSubtype.setSubtypeName("Test Subtype");

        InstUserIntfSubtypeDTO instUserIntfSubtypeDTO = instUserIntfSubtypeMapper.toInstUserIntfSubtypeDTO(instUserIntfSubtype);

        assertNotNull(instUserIntfSubtypeDTO);
        assertEquals(instUserIntfSubtype.getId(), instUserIntfSubtypeDTO.getId());
        assertEquals(instUserIntfSubtype.getSubtypeName(), instUserIntfSubtypeDTO.getSubtypeName());
    }

    @Test
    void shouldMapDTOToInstUserIntfSubtype() {
        InstUserIntfSubtypeDTO instUserIntfSubtypeDTO = new InstUserIntfSubtypeDTO();
        instUserIntfSubtypeDTO.setId(1L);
        instUserIntfSubtypeDTO.setSubtypeName("Test Subtype DTO");

        InstUserIntfSubtype instUserIntfSubtype = instUserIntfSubtypeMapper.toInstUserIntfSubtype(instUserIntfSubtypeDTO);

        assertNotNull(instUserIntfSubtype);
        assertEquals(instUserIntfSubtypeDTO.getId(), instUserIntfSubtype.getId());
        assertEquals(instUserIntfSubtypeDTO.getSubtypeName(), instUserIntfSubtype.getSubtypeName());
    }
}
------------------


import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;
import java.util.List;
import java.util.Arrays;

public class InstitutionUserIntfMapperTest {

    private final InstitutionUserIntfMapper institutionUserIntfMapper = InstitutionUserIntfMapper.INSTANCE;

    @Test
    void shouldMapInstitutionUserIntfToDTO() {
        InstitutionUserIntf institutionUserIntf = new InstitutionUserIntf();
        institutionUserIntf.setId(1L);
        institutionUserIntf.setUserName("Test User");

        InstitutionUserIntfDTO institutionUserIntfDTO = institutionUserIntfMapper.toInstitutionUserIntfDTO(institutionUserIntf);

        assertNotNull(institutionUserIntfDTO);
        assertEquals(institutionUserIntf.getId(), institutionUserIntfDTO.getId());
        assertEquals(institutionUserIntf.getUserName(), institutionUserIntfDTO.getUserName());
    }

    @Test
    void shouldMapDTOToInstitutionUserIntf() {
        InstitutionUserIntfDTO institutionUserIntfDTO = new InstitutionUserIntfDTO();
        institutionUserIntfDTO.setId(1L);
        institutionUserIntfDTO.setUserName("Test User DTO");

        InstitutionUserIntf institutionUserIntf = institutionUserIntfMapper.toInstitutionUserIntf(institutionUserIntfDTO);

        assertNotNull(institutionUserIntf);
        assertEquals(institutionUserIntfDTO.getId(), institutionUserIntf.getId());
        assertEquals(institutionUserIntfDTO.getUserName(), institutionUserIntf.getUserName());
    }

    @Test
    void shouldMapEntityListToDTOList() {
        InstitutionUserIntf institutionUserIntf = new InstitutionUserIntf();
        institutionUserIntf.setId(1L);
        institutionUserIntf.setUserName("Test User");

        List<InstitutionUserIntfDTO> institutionUserIntfDTOList = institutionUserIntfMapper.toDTOList(Arrays.asList(institutionUserIntf));

        assertNotNull(institutionUserIntfDTOList);
        assertEquals(1, institutionUserIntfDTOList.size());
        assertEquals(institutionUserIntf.getId(), institutionUserIntfDTOList.get(0).getId());
    }

    @Test
    void shouldMapDTOListToEntityList() {
        InstitutionUserIntfDTO institutionUserIntfDTO = new InstitutionUserIntfDTO();
        institutionUserIntfDTO.setId(1L);
        institutionUserIntfDTO.setUserName("Test User DTO");

        List<InstitutionUserIntf> institutionUserIntfList = institutionUserIntfMapper.toEntityList(Arrays.asList(institutionUserIntfDTO));

        assertNotNull(institutionUserIntfList);
        assertEquals(1, institutionUserIntfList.size());
        assertEquals(institutionUserIntfDTO.getId(), institutionUserIntfList.get(0).getId());
    }
}
-----------------

import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;

public class InstitutionPymMethodMapperTest {

    private final InstitutionPymMethodMapper institutionPymMethodMapper = InstitutionPymMethodMapper.INSTANCE;

    @Test
    void shouldMapInstitutionPymMethodToDTO() {
        InstitutionPymMethod institutionPymMethod = new InstitutionPymMethod();
        institutionPymMethod.setId(1L);
        institutionPymMethod.setMethodName("Credit Card");

        InstitutionPymMethodDTO institutionPymMethodDTO = institutionPymMethodMapper.toInstitutionPymMethodDTO(institutionPymMethod);

        assertNotNull(institutionPymMethodDTO);
        assertEquals(institutionPymMethod.getId(), institutionPymMethodDTO.getId());
        assertEquals(institutionPymMethod.getMethodName(), institutionPymMethodDTO.getMethodName());
    }

    @Test
    void shouldMapDTOToInstitutionPymMethod() {
        InstitutionPymMethodDTO institutionPymMethodDTO = new InstitutionPymMethodDTO();
        institutionPymMethodDTO.setId(1L);
        institutionPymMethodDTO.setMethodName("Debit Card");

        InstitutionPymMethod institutionPymMethod = institutionPymMethodMapper.toInstitutionPymMethod(institutionPymMethodDTO);

        assertNotNull(institutionPymMethod);
        assertEquals(institutionPymMethodDTO.getId(), institutionPymMethod.getId());
        assertEquals(institutionPymMethodDTO.getMethodName(), institutionPymMethod.getMethodName());
    }
}
