import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;

public class InstitutionCityMapperTest {

    private final InstitutionCityMapper institutionCityMapper = InstitutionCityMapper.INSTANCE;

    @Test
    void shouldMapInstitutionCityToDTO() {
        InstitutionCity institutionCity = new InstitutionCity();
        institutionCity.setId(1L);
        institutionCity.setCityName("Test City");

        InstitutionCityDTO institutionCityDTO = institutionCityMapper.toInstitutionCityDTO(institutionCity);

        assertNotNull(institutionCityDTO);
        assertEquals(institutionCity.getId(), institutionCityDTO.getId());
        assertEquals(institutionCity.getCityName(), institutionCityDTO.getCityName());
    }

    @Test
    void shouldMapDTOToInstitutionCity() {
        InstitutionCityDTO institutionCityDTO = new InstitutionCityDTO();
        institutionCityDTO.setId(1L);
        institutionCityDTO.setCityName("Test City DTO");

        InstitutionCity institutionCity = institutionCityMapper.toInstitutionCity(institutionCityDTO);

        assertNotNull(institutionCity);
        assertEquals(institutionCityDTO.getId(), institutionCity.getId());
        assertEquals(institutionCityDTO.getCityName(), institutionCity.getCityName());
    }
}
------------------
import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;

public class InstitutionChnnlPymMthdPscMapperTest {

    private final InstitutionChnnlPymMthdPscMapper institutionChnnlPymMthdPscMapper = InstitutionChnnlPymMthdPscMapper.INSTANCE;

    @Test
    void shouldMapInstitutionChnnlPymMthdPscToDTO() {
        InstitutionChnnlPymMthdPsc institutionChnnlPymMthdPsc = new InstitutionChnnlPymMthdPsc();
        institutionChnnlPymMthdPsc.setId(1L);
        institutionChnnlPymMthdPsc.setPscName("Test PSC");

        InstitutionChnnlPymMthdPscDTO institutionChnnlPymMthdPscDTO = institutionChnnlPymMthdPscMapper.toInstitutionChnnlPymMthdPscDTO(institutionChnnlPymMthdPsc);

        assertNotNull(institutionChnnlPymMthdPscDTO);
        assertEquals(institutionChnnlPymMthdPsc.getId(), institutionChnnlPymMthdPscDTO.getId());
        assertEquals(institutionChnnlPymMthdPsc.getPscName(), institutionChnnlPymMthdPscDTO.getPscName());
    }

    @Test
    void shouldMapDTOToInstitutionChnnlPymMthdPsc() {
        InstitutionChnnlPymMthdPscDTO institutionChnnlPymMthdPscDTO = new InstitutionChnnlPymMthdPscDTO();
        institutionChnnlPymMthdPscDTO.setId(1L);
        institutionChnnlPymMthdPscDTO.setPscName("Test PSC DTO");

        InstitutionChnnlPymMthdPsc institutionChnnlPymMthdPsc = institutionChnnlPymMthdPscMapper.toInstitutionChnnlPymMthdPsc(institutionChnnlPymMthdPscDTO);

        assertNotNull(institutionChnnlPymMthdPsc);
        assertEquals(institutionChnnlPymMthdPscDTO.getId(), institutionChnnlPymMthdPsc.getId());
        assertEquals(institutionChnnlPymMthdPscDTO.getPscName(), institutionChnnlPymMthdPsc.getPscName());
    }
}
---------------

import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;

public class InstitutionChnnlPymMthdAccMapperTest {

    private final InstitutionChnnlPymMthdAccMapper institutionChnnlPymMthdAccMapper = InstitutionChnnlPymMthdAccMapper.INSTANCE;

    @Test
    void shouldMapInstitutionChnnlPymMthdAccToDTO() {
        InstitutionChnnlPymMthdAcc institutionChnnlPymMthdAcc = new InstitutionChnnlPymMthdAcc();
        institutionChnnlPymMthdAcc.setId(1L);
        institutionChnnlPymMthdAcc.setAccountName("Test Account");

        InstitutionChnnlPymMthdAccDTO institutionChnnlPymMthdAccDTO = institutionChnnlPymMthdAccMapper.toDTO(institutionChnnlPymMthdAcc);

        assertNotNull(institutionChnnlPymMthdAccDTO);
        assertEquals(institutionChnnlPymMthdAcc.getId(), institutionChnnlPymMthdAccDTO.getId());
        assertEquals(institutionChnnlPymMthdAcc.getAccountName(), institutionChnnlPymMthdAccDTO.getAccountName());
    }

    @Test
    void shouldMapDTOToInstitutionChnnlPymMthdAcc() {
        InstitutionChnnlPymMthdAccDTO institutionChnnlPymMthdAccDTO = new InstitutionChnnlPymMthdAccDTO();
        institutionChnnlPymMthdAccDTO.setId(1L);
        institutionChnnlPymMthdAccDTO.setAccountName("Test Account DTO");

        InstitutionChnnlPymMthdAcc institutionChnnlPymMthdAcc = institutionChnnlPymMthdAccMapper.toInstitutionChnnlPymMthdAcc(institutionChnnlPymMthdAccDTO);

        assertNotNull(institutionChnnlPymMthdAcc);
        assertEquals(institutionChnnlPymMthdAccDTO.getId(), institutionChnnlPymMthdAcc.getId());
        assertEquals(institutionChnnlPymMthdAccDTO.getAccountName(), institutionChnnlPymMthdAcc.getAccountName());
    }
}
------------
import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;

public class InstitutionChannelPymMethodMapperTest {

    private final InstitutionChannelPymMethodMapper institutionChannelPymMethodMapper = InstitutionChannelPymMethodMapper.INSTANCE;

    @Test
    void shouldMapInstitutionChannelPymMethodToDTO() {
        InstitutionChannelPymMethod institutionChannelPymMethod = new InstitutionChannelPymMethod();
        institutionChannelPymMethod.setId(1L);
        institutionChannelPymMethod.setMethodName("Test Method");

        InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO = institutionChannelPymMethodMapper.toDTO(institutionChannelPymMethod);

        assertNotNull(institutionChannelPymMethodDTO);
        assertEquals(institutionChannelPymMethod.getId(), institutionChannelPymMethodDTO.getId());
        assertEquals(institutionChannelPymMethod.getMethodName(), institutionChannelPymMethodDTO.getMethodName());
    }

    @Test
    void shouldMapDTOToInstitutionChannelPymMethod() {
        InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO = new InstitutionChannelPymMethodDTO();
        institutionChannelPymMethodDTO.setId(1L);
        institutionChannelPymMethodDTO.setMethodName("Test Method DTO");

        InstitutionChannelPymMethod institutionChannelPymMethod = institutionChannelPymMethodMapper.toEntity(institutionChannelPymMethodDTO);

        assertNotNull(institutionChannelPymMethod);
        assertEquals(institutionChannelPymMethodDTO.getId(), institutionChannelPymMethod.getId());
        assertEquals(institutionChannelPymMethodDTO.getMethodName(), institutionChannelPymMethod.getMethodName());
    }
}
-----------------
import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;

public class InstitutionChannelProcessMapperTest {

    private final InstitutionChannelProcessMapper institutionChannelProcessMapper = InstitutionChannelProcessMapper.INSTANCE;

    @Test
    void shouldMapInstitutionChannelProcessToDTO() {
        InstitutionChannelProcess institutionChannelProcess = new InstitutionChannelProcess();
        institutionChannelProcess.setId(1L);
        institutionChannelProcess.setProcessName("Test Process");

        InstitutionChannelProcessDTO institutionChannelProcessDTO = institutionChannelProcessMapper.toInstitutionChannelProcessDTO(institutionChannelProcess);

        assertNotNull(institutionChannelProcessDTO);
        assertEquals(institutionChannelProcess.getId(), institutionChannelProcessDTO.getId());
        assertEquals(institutionChannelProcess.getProcessName(), institutionChannelProcessDTO.getProcessName());
    }

    @Test
    void shouldMapDTOToInstitutionChannelProcess() {
        InstitutionChannelProcessDTO institutionChannelProcessDTO = new InstitutionChannelProcessDTO();
        institutionChannelProcessDTO.setId(1L);
        institutionChannelProcessDTO.setProcessName("Test Process DTO");

        InstitutionChannelProcess institutionChannelProcess = institutionChannelProcessMapper.toInstitutionChannelProcess(institutionChannelProcessDTO);

        assertNotNull(institutionChannelProcess);
        assertEquals(institutionChannelProcessDTO.getId(), institutionChannelProcess.getId());
        assertEquals(institutionChannelProcessDTO.getProcessName(), institutionChannelProcess.getProcessName());
    }
}
