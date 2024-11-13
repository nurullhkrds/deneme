import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import com.ykb.architecture.micro.error.exception.DataConflictException;
import com.ykb.architecture.micro.error.exception.DataNotFoundException;
import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.common.exception.BillExceptionsUI;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionChnnlPymMthdPscMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionChnlPymMethodService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionChnlPymMthdPscRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionChnlPymMthdPscRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.response.InstitutionChnlPymMthdPscWebDTO;
import com.ykb.payments.bill.transaction.institution.domain.InstitutionChnnlPymMthdPsc;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionChannelPymMethodDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionChnnlPymMthdPscDTO;
import com.ykb.payments.bill.transaction.institution.repository.InstitutionChnnlPymMthdPscRepository;

import java.time.LocalDateTime;
import java.util.List;
import java.util.Optional;
import java.util.Arrays;

class AdminInstitutionChnlPymMthdPscServiceImplTest {

    @Mock
    private InstitutionChnnlPymMthdPscRepository institutionChnnlPymMthdPscRepository;

    @Mock
    private AdminInstitutionChnnlPymMthdPscMapper institutionChnnlPymMthdPscMapper;

    @Mock
    private AdminInstitutionChnlPymMethodService institutionChnlPymMethodService;

    @InjectMocks
    private AdminInstitutionChnlPymMthdPscServiceImpl adminInstitutionChnlPymMthdPscService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getAllInstitutionChnlPymMthdAPsc_shouldReturnListOfInstitutionChnlPymMthdPscWebDTO() {
        List<Object[]> results = Arrays.asList(new Object[]{}, new Object[]{});
        List<InstitutionChnlPymMthdPscWebDTO> expectedDTOs = Arrays.asList(new InstitutionChnlPymMthdPscWebDTO(), new InstitutionChnlPymMthdPscWebDTO());

        when(institutionChnnlPymMthdPscRepository.findInstitutionChnlPymMthdAccWithInstitution()).thenReturn(results);
        when(institutionChnnlPymMthdPscMapper.objectArrayToWebDTO(any())).thenReturn(expectedDTOs.get(0), expectedDTOs.get(1));

        List<InstitutionChnlPymMthdPscWebDTO> result = adminInstitutionChnlPymMthdPscService.getAllInstitutionChnlPymMthdAPsc();

        assertNotNull(result);
        assertEquals(2, result.size());
        verify(institutionChnnlPymMthdPscRepository, times(1)).findInstitutionChnlPymMthdAccWithInstitution();
        verify(institutionChnnlPymMthdPscMapper, times(2)).objectArrayToWebDTO(any());
    }

    @Test
    void getInstitutionChnlPymMthdAPscById_shouldReturnInstitutionChnnlPymMthdPscDTO_whenFound() {
        Long id = 1L;
        InstitutionChnnlPymMthdPsc institutionChnnlPymMthdPsc = new InstitutionChnnlPymMthdPsc();
        InstitutionChnnlPymMthdPscDTO expectedDTO = new InstitutionChnnlPymMthdPscDTO();

        when(institutionChnnlPymMthdPscRepository.findById(id)).thenReturn(Optional.of(institutionChnnlPymMthdPsc));
        when(institutionChnnlPymMthdPscMapper.toDTO(institutionChnnlPymMthdPsc)).thenReturn(expectedDTO);

        InstitutionChnnlPymMthdPscDTO result = adminInstitutionChnlPymMthdPscService.getInstitutionChnlPymMthdAPscById(id);

        assertNotNull(result);
        assertEquals(expectedDTO, result);
        verify(institutionChnnlPymMthdPscRepository, times(1)).findById(id);
        verify(institutionChnnlPymMthdPscMapper, times(1)).toDTO(institutionChnnlPymMthdPsc);
    }

    @Test
    void getInstitutionChnlPymMthdAPscById_shouldReturnNull_whenNotFound() {
        Long id = 1L;

        when(institutionChnnlPymMthdPscRepository.findById(id)).thenReturn(Optional.empty());

        InstitutionChnnlPymMthdPscDTO result = adminInstitutionChnlPymMthdPscService.getInstitutionChnlPymMthdAPscById(id);

        assertNull(result);
        verify(institutionChnnlPymMthdPscRepository, times(1)).findById(id);
    }

    @Test
    void createInstitutionChnlPymMthdPsc_shouldThrowDataConflictException_whenInstitutionChnlPymMthdPscAlreadyExists() {
        CreateInstitutionChnlPymMthdPscRequestDTO requestDTO = new CreateInstitutionChnlPymMthdPscRequestDTO();
        requestDTO.setInstitutionChannelPymMethodId(1L);

        when(institutionChnnlPymMthdPscRepository.existsByInstitutionChannelPymMethodId(requestDTO.getInstitutionChannelPymMethodId()))
                .thenReturn(true);

        assertThrows(DataConflictException.class, () -> adminInstitutionChnlPymMthdPscService.createInstitutionChnlPymMthdPsc(requestDTO));
        verify(institutionChnnlPymMthdPscRepository, times(1)).existsByInstitutionChannelPymMethodId(requestDTO.getInstitutionChannelPymMethodId());
    }

    @Test
    void createInstitutionChnlPymMthdPsc_shouldThrowDataNotFoundException_whenInstitutionChannelPymMethodNotFound() throws MicroException {
        CreateInstitutionChnlPymMthdPscRequestDTO requestDTO = new CreateInstitutionChnlPymMthdPscRequestDTO();
        requestDTO.setInstitutionChannelPymMethodId(1L);

        when(institutionChnnlPymMthdPscRepository.existsByInstitutionChannelPymMethodId(requestDTO.getInstitutionChannelPymMethodId()))
                .thenReturn(false);
        when(institutionChnlPymMethodService.getInstitutionChannelPymMethodById(requestDTO.getInstitutionChannelPymMethodId())).thenReturn(null);

        assertThrows(DataNotFoundException.class, () -> adminInstitutionChnlPymMthdPscService.createInstitutionChnlPymMthdPsc(requestDTO));
        verify(institutionChnlPymMethodService, times(1)).getInstitutionChannelPymMethodById(requestDTO.getInstitutionChannelPymMethodId());
    }

    @Test
    void createInstitutionChnlPymMthdPsc_shouldReturnInstitutionChnnlPymMthdPscDTO_whenSuccessful() throws MicroException {
        CreateInstitutionChnlPymMthdPscRequestDTO requestDTO = new CreateInstitutionChnlPymMthdPscRequestDTO();
        requestDTO.setInstitutionChannelPymMethodId(1L);
        InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO = new InstitutionChannelPymMethodDTO();
        InstitutionChnnlPymMthdPscDTO institutionChnnlPymMthdPscDTO = new InstitutionChnnlPymMthdPscDTO();
        InstitutionChnnlPymMthdPsc institutionChnnlPymMthdPsc = new InstitutionChnnlPymMthdPsc();

        when(institutionChnnlPymMthdPscRepository.existsByInstitutionChannelPymMethodId(requestDTO.getInstitutionChannelPymMethodId()))
                .thenReturn(false);
        when(institutionChnlPymMethodService.getInstitutionChannelPymMethodById(requestDTO.getInstitutionChannelPymMethodId())).thenReturn(institutionChannelPymMethodDTO);
        when(institutionChnnlPymMthdPscMapper.toDTO(requestDTO)).thenReturn(institutionChnnlPymMthdPscDTO);
        when(institutionChnnlPymMthdPscMapper.toInstitutionChnnlPymMthdPsc(institutionChnnlPymMthdPscDTO)).thenReturn(institutionChnnlPymMthdPsc);
        when(institutionChnnlPymMthdPscRepository.save(institutionChnnlPymMthdPsc)).thenReturn(institutionChnnlPymMthdPsc);
        when(institutionChnnlPymMthdPscMapper.toDTO(institutionChnnlPymMthdPsc)).thenReturn(institutionChnnlPymMthdPscDTO);

        InstitutionChnnlPymMthdPscDTO result = adminInstitutionChnlPymMthdPscService.createInstitutionChnlPymMthdPsc(requestDTO);

        assertNotNull(result);
        assertEquals(institutionChnnlPymMthdPscDTO, result);
        verify(institutionChnnlPymMthdPscRepository, times(1)).existsByInstitutionChannelPymMethodId(requestDTO.getInstitutionChannelPymMethodId());
        verify(institutionChnlPymMethodService, times(1)).getInstitutionChannelPymMethodById(requestDTO.getInstitutionChannelPymMethodId());
        verify(institutionChnnlPymMthdPscMapper, times(1)).toDTO(requestDTO);
        verify(institutionChnnlPymMthdPscMapper, times(1)).toInstitutionChnnlPymMthdPsc(institutionChnnlPymMthdPscDTO);
        verify(institutionChnnlPymMthdPscRepository, times(1)).save(institutionChnnlPymMthdPsc);
        verify(institutionChnnlPymMthdPscMapper, times(1)).toDTO(institutionChnnlPymMthdPsc);
    }

    @Test
    void updateInstitutionChnlPymMthdPsc_shouldThrowDataNotFoundException_whenInstitutionChnlPymMthdPscDoesNotExist() {
        UpdateInstitutionChnlPymMthdPscRequestDTO requestDTO = new UpdateInstitutionChnlPymMthdPscRequestDTO();
        requestDTO.setId(1L);

        when(institutionChnnlPymMthdPscRepository.findById(requestDTO.getId())).thenReturn(Optional.empty());

        assertThrows(DataNotFoundException.class, () -> adminInstitutionChnlPymMthdPscService.updateInstitutionChnlPymMthdPsc(requestDTO));
        verify(institutionChnnlPymMthdPscRepository, times(1)).findById(requestDTO.getId());
    }

    @Test
    void updateInstitutionChnlPymMthdPsc_shouldReturnUpdatedInstitutionChnnlPymMthdPscDTO_whenSuccessful() throws MicroException {
        UpdateInstitutionChnlPymMthdPscRequestDTO requestDTO = new UpdateInstitutionChnlPymMthdPscRequestDTO();
        requestDTO.setId(1L);
        requestDTO.setInstitutionChannelPymMethodId(1L);
        requestDTO.setMondayBlockDayCount(3);
        requestDTO.setFridayBlockDayCount(2);
        requestDTO.setSaturdayBlockDayCount(1);
        requestDTO.setTuesdayBlockDayCount(4);
        requestDTO.setWednesdayBlockDayCount(5);
        requestDTO.setThursdayBlockDayCount(6);
        requestDTO.setSundayBlockDayCount(0);
        requestDTO.setUpdateUser("user");

        InstitutionChnnlPymMthdPsc existingInstitutionChnnlPymMthdPsc = new InstitutionChnnlPymMthdPsc();
        InstitutionChnnlPymMthdPscDTO existingInstitutionChnnlPymMthdPscDTO = new InstitutionChnnlPymMthdPscDTO();
        InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO = new InstitutionChannelPymMethodDTO();
        InstitutionChnnlPymMthdPsc updatedInstitutionChnnlPymMthdPsc = new InstitutionChnnlPymMthdPsc();
        InstitutionChnnlPymMthdPscDTO updatedInstitutionChnnlPymMthdPscDTO = new InstitutionChnnlPymMthdPscDTO();

        when(institutionChnnlPymMthdPscRepository.findById(requestDTO.getId())).thenReturn(Optional.of(existingInstitutionChnnlPymMthdPsc));
        when(institutionChnlPymMethodService.getInstitutionChannelPymMethodById(requestDTO.getInstitutionChannelPymMethodId())).thenReturn(institutionChannelPymMethodDTO);
        when(institutionChnnlPymMthdPscMapper.toDTO(existingInstitutionChnnlPymMthdPsc)).thenReturn(existingInstitutionChnnlPymMthdPscDTO);
        when(institutionChnnlPymMthdPscMapper.toInstitutionChnnlPymMthdPsc(existingInstitutionChnnlPymMthdPscDTO)).thenReturn(updatedInstitutionChnnlPymMthdPsc);
        when(institutionChnnlPymMthdPscRepository.save(updatedInstitutionChnnlPymMthdPsc)).thenReturn(updatedInstitutionChnnlPymMthdPsc);
        when(institutionChnnlPymMthdPscMapper.toDTO(updatedInstitutionChnnlPymMthdPsc)).thenReturn(updatedInstitutionChnnlPymMthdPscDTO);

        InstitutionChnnlPymMthdPscDTO result = adminInstitutionChnlPymMthdPscService.updateInstitutionChnlPymMthdPsc(requestDTO);

        assertNotNull(result);
        assertEquals(updatedInstitutionChnnlPymMthdPscDTO, result);
        verify(institutionChnnlPymMthdPscRepository, times(1)).findById(requestDTO.getId());
        verify(institutionChnlPymMethodService, times(1)).getInstitutionChannelPymMethodById(requestDTO.getInstitutionChannelPymMethodId());
        verify(institutionChnnlPymMthdPscMapper, times(1)).toDTO(existingInstitutionChnnlPymMthdPsc);
        verify(institutionChnnlPymMthdPscMapper, times(1)).toInstitutionChnnlPymMthdPsc(existingInstitutionChnnlPymMthdPscDTO);
        verify(institutionChnnlPymMthdPscRepository, times(1)).save(updatedInstitutionChnnlPymMthdPsc);
        verify(institutionChnnlPymMthdPscMapper, times(1)).toDTO(updatedInstitutionChnnlPymMthdPsc);
    }
}
