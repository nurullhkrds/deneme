import org.junit.jupiter.api.Test;
import org.mapstruct.factory.Mappers;

import static org.junit.jupiter.api.Assertions.*;

public class InstitutionChannelProcessMapperTest {

    private final InstitutionChannelProcessMapper mapper = Mappers.getMapper(InstitutionChannelProcessMapper.class);

    @Test
    public void testToDTO() {
        InstitutionChannelProcess process = new InstitutionChannelProcess();
        process.setId(1L);
        process.setName("Test Process");

        InstitutionChannelProcessDTO dto = mapper.toInstitutionChannelProcessDTO(process);

        assertNotNull(dto);
        assertEquals(process.getId(), dto.getId());
        assertEquals(process.getName(), dto.getName());
    }

    @Test
    public void testToEntity() {
        InstitutionChannelProcessDTO dto = new InstitutionChannelProcessDTO();
        dto.setId(1L);
        dto.setName("Test Process DTO");

        InstitutionChannelProcess process = mapper.toInstitutionChannelProcess(dto);

        assertNotNull(process);
        assertEquals(dto.getId(), process.getId());
        assertEquals(dto.getName(), process.getName());
    }
}


import org.junit.jupiter.api.Test;
import org.mapstruct.factory.Mappers;

import static org.junit.jupiter.api.Assertions.*;

public class InstitutionChannelPymMethodMapperTest {

    private final InstitutionChannelPymMethodMapper mapper = Mappers.getMapper(InstitutionChannelPymMethodMapper.class);

    @Test
    public void testToDTO() {
        InstitutionChannelPymMethod method = new InstitutionChannelPymMethod();
        method.setId(1L);
        method.setMethodName("Test Method");

        InstitutionChannelPymMethodDTO dto = mapper.toDTO(method);

        assertNotNull(dto);
        assertEquals(method.getId(), dto.getId());
        assertEquals(method.getMethodName(), dto.getMethodName());
    }

    @Test
    public void testToEntity() {
        InstitutionChannelPymMethodDTO dto = new InstitutionChannelPymMethodDTO();
        dto.setId(1L);
        dto.setMethodName("Test Method DTO");

        InstitutionChannelPymMethod method = mapper.toEntity(dto);

        assertNotNull(method);
        assertEquals(dto.getId(), method.getId());
        assertEquals(dto.getMethodName(), method.getMethodName());
    }
}
import org.junit.jupiter.api.Test;
import org.mapstruct.factory.Mappers;

import static org.junit.jupiter.api.Assertions.*;

public class InstitutionChnnlPymMthdAccMapperTest {

    private final InstitutionChnnlPymMthdAccMapper mapper = Mappers.getMapper(InstitutionChnnlPymMthdAccMapper.class);

    @Test
    public void testToDTO() {
        InstitutionChnnlPymMthdAcc acc = new InstitutionChnnlPymMthdAcc();
        acc.setId(1L);
        acc.setAccountName("Test Account");

        InstitutionChnnlPymMthdAccDTO dto = mapper.toDTO(acc);

        assertNotNull(dto);
        assertEquals(acc.getId(), dto.getId());
        assertEquals(acc.getAccountName(), dto.getAccountName());
    }

    @Test
    public void testToEntity() {
        InstitutionChnnlPymMthdAccDTO dto = new InstitutionChnnlPymMthdAccDTO();
        dto.setId(1L);
        dto.setAccountName("Test Account DTO");

        InstitutionChnnlPymMthdAcc acc = mapper.toInstitutionChnnlPymMthdAcc(dto);

        assertNotNull(acc);
        assertEquals(dto.getId(), acc.getId());
        assertEquals(dto.getAccountName(), acc.getAccountName());
    }
}
import org.junit.jupiter.api.Test;
import org.mapstruct.factory.Mappers;

import static org.junit.jupiter.api.Assertions.*;

public class InstitutionChnnlPymMthdPscMapperTest {

    private final InstitutionChnnlPymMthdPscMapper mapper = Mappers.getMapper(InstitutionChnnlPymMthdPscMapper.class);

    @Test
    public void testToDTO() {
        InstitutionChnnlPymMthdPsc psc = new InstitutionChnnlPymMthdPsc();
        psc.setId(1L);
        psc.setPscName("Test PSC");

        InstitutionChnnlPymMthdPscDTO dto = mapper.toInstitutionChnnlPymMthdPscDTO(psc);

        assertNotNull(dto);
        assertEquals(psc.getId(), dto.getId());
        assertEquals(psc.getPscName(), dto.getPscName());
    }

    @Test
    public void testToEntity() {
        InstitutionChnnlPymMthdPscDTO dto = new InstitutionChnnlPymMthdPscDTO();
        dto.setId(1L);
        dto.setPscName("Test PSC DTO");

        InstitutionChnnlPymMthdPsc psc = mapper.toInstitutionChnnlPymMthdPsc(dto);

        assertNotNull(psc);
        assertEquals(dto.getId(), psc.getId());
        assertEquals(dto.getPscName(), psc.getPscName());
    }
}
