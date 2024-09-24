import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;

public class InstitutionProcessMapperTest {

    private final InstitutionProcessMapper institutionProcessMapper = InstitutionProcessMapper.INSTANCE;

    @Test
    void shouldMapInstitutionProcessToDTO() {
        InstitutionProcess institutionProcess = new InstitutionProcess();
        institutionProcess.setId(1L);
        institutionProcess.setProcessName("Test Process");

        InstitutionProcessDTO institutionProcessDTO = institutionProcessMapper.toInstitutionProcessDTO(institutionProcess);

        assertNotNull(institutionProcessDTO);
        assertEquals(institutionProcess.getId(), institutionProcessDTO.getId());
        assertEquals(institutionProcess.getProcessName(), institutionProcessDTO.getProcessName());
    }

    @Test
    void shouldMapDTOToInstitutionProcess() {
        InstitutionProcessDTO institutionProcessDTO = new InstitutionProcessDTO();
        institutionProcessDTO.setId(1L);
        institutionProcessDTO.setProcessName("Test Process DTO");

        InstitutionProcess institutionProcess = institutionProcessMapper.toInstitutionProcess(institutionProcessDTO);

        assertNotNull(institutionProcess);
        assertEquals(institutionProcessDTO.getId(), institutionProcess.getId());
        assertEquals(institutionProcessDTO.getProcessName(), institutionProcess.getProcessName());
    }
}
----------------

import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;

public class InstitutionOrderPymMethodMapperTest {

    private final InstitutionOrderPymMethodMapper institutionOrderPymMethodMapper = InstitutionOrderPymMethodMapper.INSTANCE;

    @Test
    void shouldMapInstitutionOrderPymMethodToDTO() {
        InstitutionOrderPymMethod institutionOrderPymMethod = new InstitutionOrderPymMethod();
        institutionOrderPymMethod.setId(1L);
        institutionOrderPymMethod.setMethodName("Test Method");

        InstitutionOrderPymMethodDTO institutionOrderPymMethodDTO = institutionOrderPymMethodMapper.toInstitutionOrderPymMethodDTO(institutionOrderPymMethod);

        assertNotNull(institutionOrderPymMethodDTO);
        assertEquals(institutionOrderPymMethod.getId(), institutionOrderPymMethodDTO.getId());
        assertEquals(institutionOrderPymMethod.getMethodName(), institutionOrderPymMethodDTO.getMethodName());
    }

    @Test
    void shouldMapDTOToInstitutionOrderPymMethod() {
        InstitutionOrderPymMethodDTO institutionOrderPymMethodDTO = new InstitutionOrderPymMethodDTO();
        institutionOrderPymMethodDTO.setId(1L);
        institutionOrderPymMethodDTO.setMethodName("Test Method DTO");

        InstitutionOrderPymMethod institutionOrderPymMethod = institutionOrderPymMethodMapper.toInstitutionOrderPymMethod(institutionOrderPymMethodDTO);

        assertNotNull(institutionOrderPymMethod);
        assertEquals(institutionOrderPymMethodDTO.getId(), institutionOrderPymMethod.getId());
        assertEquals(institutionOrderPymMethodDTO.getMethodName(), institutionOrderPymMethod.getMethodName());
    }
}
------------------------

import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;

public class InstitutionMapperTest {

    private final InstitutionMapper institutionMapper = InstitutionMapper.INSTANCE;

    @Test
    void shouldMapInstitutionToDTO() {
        Institution institution = new Institution();
        institution.setId(1L);
        institution.setName("Test Institution");

        InstitutionDTO institutionDTO = institutionMapper.toInstitutionDTO(institution);

        assertNotNull(institutionDTO);
        assertEquals(institution.getId(), institutionDTO.getId());
        assertEquals(institution.getName(), institutionDTO.getName());
    }

    @Test
    void shouldMapDTOToInstitution() {
        InstitutionDTO institutionDTO = new InstitutionDTO();
        institutionDTO.setId(1L);
        institutionDTO.setName("Test Institution DTO");

        Institution institution = institutionMapper.toInstitution(institutionDTO);

        assertNotNull(institution);
        assertEquals(institutionDTO.getId(), institution.getId());
        assertEquals(institutionDTO.getName(), institution.getName());
    }
}
-----------

import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;
import java.util.List;
import java.util.Arrays;

public class InstitutionFeatureMapperTest {

    private final InstitutionFeatureMapper institutionFeatureMapper = InstitutionFeatureMapper.INSTANCE;

    @Test
    void shouldMapInstitutionFeatureToDTO() {
        InstitutionFeature institutionFeature = new InstitutionFeature();
        institutionFeature.setId(1L);
        institutionFeature.setFeatureName("Test Feature");

        InstitutionFeatureDTO institutionFeatureDTO = institutionFeatureMapper.toInstitutionFeatureDTO(institutionFeature);

        assertNotNull(institutionFeatureDTO);
        assertEquals(institutionFeature.getId(), institutionFeatureDTO.getId());
        assertEquals(institutionFeature.getFeatureName(), institutionFeatureDTO.getFeatureName());
    }

    @Test
    void shouldMapDTOToInstitutionFeature() {
        InstitutionFeatureDTO institutionFeatureDTO = new InstitutionFeatureDTO();
        institutionFeatureDTO.setId(1L);
        institutionFeatureDTO.setFeatureName("Test Feature DTO");

        InstitutionFeature institutionFeature = institutionFeatureMapper.toInstitutionFeature(institutionFeatureDTO);

        assertNotNull(institutionFeature);
        assertEquals(institutionFeatureDTO.getId(), institutionFeature.getId());
        assertEquals(institutionFeatureDTO.getFeatureName(), institutionFeature.getFeatureName());
    }

    @Test
    void shouldMapEntityListToDTOList() {
        InstitutionFeature institutionFeature = new InstitutionFeature();
        institutionFeature.setId(1L);
        institutionFeature.setFeatureName("Test Feature");

        List<InstitutionFeatureDTO> institutionFeatureDTOList = institutionFeatureMapper.toInstitutionFeatureDTOList(Arrays.asList(institutionFeature));

        assertNotNull(institutionFeatureDTOList);
        assertEquals(1, institutionFeatureDTOList.size());
        assertEquals(institutionFeature.getId(), institutionFeatureDTOList.get(0).getId());
    }
}
-------------

import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;

public class InstitutionDebtTypeMapperTest {

    private final InstitutionDebtTypeMapper institutionDebtTypeMapper = InstitutionDebtTypeMapper.INSTANCE;

    @Test
    void shouldMapInstitutionDebtTypeToDTO() {
        InstitutionDebtType institutionDebtType = new InstitutionDebtType();
        institutionDebtType.setId(1L);
        institutionDebtType.setDebtTypeName("Test Debt Type");

        InstitutionDebtTypeDTO institutionDebtTypeDTO = institutionDebtTypeMapper.toDTO(institutionDebtType);

        assertNotNull(institutionDebtTypeDTO);
        assertEquals(institutionDebtType.getId(), institutionDebtTypeDTO.getId());
        assertEquals(institutionDebtType.getDebtTypeName(), institutionDebtTypeDTO.getDebtTypeName());
    }

    @Test
    void shouldMapDTOToInstitutionDebtType() {
        InstitutionDebtTypeDTO institutionDebtTypeDTO = new InstitutionDebtTypeDTO();
        institutionDebtTypeDTO.setId(1L);
        institutionDebtTypeDTO.setDebtTypeName("Test Debt Type DTO");

        InstitutionDebtType institutionDebtType = institutionDebtTypeMapper.toEntity(institutionDebtTypeDTO);

        assertNotNull(institutionDebtType);
        assertEquals(institutionDebtTypeDTO.getId(), institutionDebtType.getId());
        assertEquals(institutionDebtTypeDTO.getDebtTypeName(), institutionDebtType.getDebtTypeName());
    }
}
---------------

import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.Test;

public class InstitutionCommunicationMapperTest {

    private final InstitutionCommunicationMapper institutionCommunicationMapper = InstitutionCommunicationMapper.INSTANCE;

    @Test
    void shouldMapInstitutionCommunicationToDTO() {
        InstitutionCommunication institutionCommunication = new InstitutionCommunication();
        institutionCommunication.setId(1L);
        institutionCommunication.setCommunicationType("Test Communication");

        InstitutionCommunicationDTO institutionCommunicationDTO = institutionCommunicationMapper.toInstitutionCommunicationDTO(institutionCommunication);

        assertNotNull(institutionCommunicationDTO);
        assertEquals(institutionCommunication.getId(), institutionCommunicationDTO.getId());
        assertEquals(institutionCommunication.getCommunicationType(), institutionCommunicationDTO.getCommunicationType());
    }

    @Test
    void shouldMapDTOToInstitutionCommunication() {
        InstitutionCommunicationDTO institutionCommunicationDTO = new InstitutionCommunicationDTO();
        institutionCommunicationDTO.setId(1L);
        institutionCommunicationDTO.setCommunicationType("Test Communication DTO");

        InstitutionCommunication institutionCommunication = institutionCommunicationMapper.toInstitutionCommunication(institutionCommunicationDTO);

        assertNotNull(institutionCommunication);
        assertEquals(institutionCommunicationDTO.getId(), institutionCommunication.getId());
        assertEquals(institutionCommunicationDTO.getCommunicationType(), institutionCommunication.getCommunicationType());
    }
}
