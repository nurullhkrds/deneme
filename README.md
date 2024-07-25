import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.transaction.annotation.Transactional;

import java.util.*;

@ExtendWith(MockitoExtension.class)
class ProvisionServiceImplTest {

    @Mock
    private ProvisionRepository provisionRepository;

    @Mock
    private ProvisionMapper provisionMapper;

    @InjectMocks
    private ProvisionServiceImpl provisionService;

    private Provision provision;
    private ProvisionDTO provisionDTO;

    @BeforeEach
    void setUp() {
        provision = new Provision();
        provisionDTO = new ProvisionDTO();
    }

    @Test
    void getProvisionRecord_ShouldReturnDTO_WhenEntityExists() {
        Long id = 1L;
        when(provisionRepository.findById(id)).thenReturn(Optional.of(provision));
        when(provisionMapper.toDTO(provision)).thenReturn(provisionDTO);

        ProvisionDTO result = provisionService.getProvisionRecord(id);

        assertNotNull(result);
        assertEquals(provisionDTO, result);
    }

    @Test
    void getProvisionRecord_ShouldReturnNull_WhenEntityDoesNotExist() {
        Long id = 1L;
        when(provisionRepository.findById(id)).thenReturn(Optional.empty());

        ProvisionDTO result = provisionService.getProvisionRecord(id);

        assertNull(result);
    }

    @Test
    @Transactional
    void createProvisions_ShouldReturnDTOList_WhenValidDTOListProvided() {
        List<ProvisionDTO> provisionDTOList = Collections.singletonList(provisionDTO);
        List<Provision> provisionList = Collections.singletonList(provision);
        when(provisionMapper.toEntityList(provisionDTOList)).thenReturn(provisionList);
        when(provisionRepository.saveAll(provisionList)).thenReturn(provisionList);
        when(provisionMapper.toDTOList(provisionList)).thenReturn(provisionDTOList);

        List<ProvisionDTO> result = provisionService.createProvisions(provisionDTOList);

        assertNotNull(result);
        assertEquals(provisionDTOList, result);
    }

    @Test
    @Transactional
    void updateCommissionData_ShouldCallRepositoryUpdate() {
        String commissionData = "newCommissionData";
        Long id = 1L;

        provisionService.updateCommissionData(commissionData, id);

        verify(provisionRepository).updateCommissionData(commissionData, id);
    }

    @Test
    @Transactional
    void invalidateNotPaidProvisions_ShouldUpdateStatus_WhenProvisionsFound() {
        Long institutionDebtTypeId = 1L;
        String subscriberNo = "subscriberNo";
        List<Provision> provisions = Arrays.asList(provision);
        when(provisionRepository.findBynDebtTypeIdAndSubscriberNoWitLock(institutionDebtTypeId, subscriberNo))
                .thenReturn(Optional.of(provisions));

        provisionService.invalidateNotPaidProvisions(institutionDebtTypeId, subscriberNo);

        provisions.forEach(p -> assertEquals(EnumProvisionStatus.CANCELLED, p.getStatus()));
        verify(provisionRepository).saveAll(provisions);
    }

    @Test
    @Transactional
    void invalidateNotPaidProvisions_ShouldNotUpdateStatus_WhenNoProvisionsFound() {
        Long institutionDebtTypeId = 1L;
        String subscriberNo = "subscriberNo";
        when(provisionRepository.findBynDebtTypeIdAndSubscriberNoWitLock(institutionDebtTypeId, subscriberNo))
                .thenReturn(Optional.empty());

        provisionService.invalidateNotPaidProvisions(institutionDebtTypeId, subscriberNo);

        verify(provisionRepository, never()).saveAll(any());
    }

    @Test
    void updateProvision_ShouldSaveEntity() {
        when(provisionMapper.toEntity(provisionDTO)).thenReturn(provision);

        provisionService.updateProvision(provisionDTO);

        verify(provisionRepository).save(provision);
    }
}
