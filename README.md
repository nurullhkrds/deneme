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
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionChnnlPymMthdAccMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionChnlPymMethodService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionChnlPymMthdAccRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionChnlPymMthdAccRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.response.InstitutionChnlPymMthdAccWebDTO;
import com.ykb.payments.bill.transaction.institution.domain.InstitutionChnnlPymMthdAcc;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionChannelPymMethodDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionChnnlPymMthdAccDTO;
import com.ykb.payments.bill.transaction.institution.repository.InstitutionChnnlPymMthdAccRepository;

import java.time.LocalDateTime;
import java.util.List;
import java.util.Optional;
import java.util.Arrays;

class AdminInstitutionChnlPymMthdAccServiceImplTest {

    @Mock
    private InstitutionChnnlPymMthdAccRepository institutionChnnlPymMthdAccRepository;

    @Mock
    private AdminInstitutionChnnlPymMthdAccMapper institutionChnnlPymMthdAccMapper;

    @Mock
    private AdminInstitutionChnlPymMethodService institutionChnlPymMethodService;

    @InjectMocks
    private AdminInstitutionChnlPymMthdAccServiceImpl adminInstitutionChnlPymMthdAccService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getAllInstitutionChannelPymMethodsAcc_shouldReturnListOfInstitutionChnlPymMthdAccWebDTO() {
        List<Object[]> results = Arrays.asList(new Object[]{}, new Object[]{});
        List<InstitutionChnlPymMthdAccWebDTO> expectedDTOs = Arrays.asList(new InstitutionChnlPymMthdAccWebDTO(), new InstitutionChnlPymMthdAccWebDTO());

        when(institutionChnnlPymMthdAccRepository.findInstitutionChnlPymMthdAccWithInstitution()).thenReturn(results);
        when(institutionChnnlPymMthdAccMapper.objectArrayToWebDTO(any())).thenReturn(expectedDTOs.get(0), expectedDTOs.get(1));

        List<InstitutionChnlPymMthdAccWebDTO> result = adminInstitutionChnlPymMthdAccService.getAllInstitutionChannelPymMethodsAcc();

        assertNotNull(result);
        assertEquals(2, result.size());
        verify(institutionChnnlPymMthdAccRepository, times(1)).findInstitutionChnlPymMthdAccWithInstitution();
        verify(institutionChnnlPymMthdAccMapper, times(2)).objectArrayToWebDTO(any());
    }

    @Test
    void getInstitutionChannelPymMethodAccById_shouldReturnInstitutionChnnlPymMthdAccDTO_whenFound() {
        Long id = 1L;
        InstitutionChnnlPymMthdAcc institutionChnnlPymMthdAcc = new InstitutionChnnlPymMthdAcc();
        InstitutionChnnlPymMthdAccDTO expectedDTO = new InstitutionChnnlPymMthdAccDTO();

        when(institutionChnnlPymMthdAccRepository.findById(id)).thenReturn(Optional.of(institutionChnnlPymMthdAcc));
        when(institutionChnnlPymMthdAccMapper.toDTO(institutionChnnlPymMthdAcc)).thenReturn(expectedDTO);

        InstitutionChnnlPymMthdAccDTO result = adminInstitutionChnlPymMthdAccService.getInstitutionChannelPymMethodAccById(id);

        assertNotNull(result);
        assertEquals(expectedDTO, result);
        verify(institutionChnnlPymMthdAccRepository, times(1)).findById(id);
        verify(institutionChnnlPymMthdAccMapper, times(1)).toDTO(institutionChnnlPymMthdAcc);
    }

    @Test
    void getInstitutionChannelPymMethodAccById_shouldReturnNull_whenNotFound() {
        Long id = 1L;

        when(institutionChnnlPymMthdAccRepository.findById(id)).thenReturn(Optional.empty());

        InstitutionChnnlPymMthdAccDTO result = adminInstitutionChnlPymMthdAccService.getInstitutionChannelPymMethodAccById(id);

        assertNull(result);
        verify(institutionChnnlPymMthdAccRepository, times(1)).findById(id);
    }

    @Test
    void createInstitutionChannelPymMethodAcc_shouldThrowDataConflictException_whenInstitutionChannelPymMethodAccAlreadyExists() {
        CreateInstitutionChnlPymMthdAccRequestDTO requestDTO = new CreateInstitutionChnlPymMthdAccRequestDTO();
        requestDTO.setInstitutionChannelPymMethodId(1L);
        requestDTO.setCurrency("USD");
        requestDTO.setCollectionAccountNo("123456");

        when(institutionChnnlPymMthdAccRepository.existsByInstitutionChannelPymMethodIdAndCurrency(requestDTO.getInstitutionChannelPymMethodId(), requestDTO.getCurrency()))
                .thenReturn(true);

        assertThrows(DataConflictException.class, () -> adminInstitutionChnlPymMthdAccService.createInstitutionChannelPymMethodAcc(requestDTO));
        verify(institutionChnnlPymMthdAccRepository, times(1)).existsByInstitutionChannelPymMethodIdAndCurrency(requestDTO.getInstitutionChannelPymMethodId(), requestDTO.getCurrency());
    }

    @Test
    void createInstitutionChannelPymMethodAcc_shouldThrowDataNotFoundException_whenInstitutionChannelPymMethodNotFound() throws MicroException {
        CreateInstitutionChnlPymMthdAccRequestDTO requestDTO = new CreateInstitutionChnlPymMthdAccRequestDTO();
        requestDTO.setInstitutionChannelPymMethodId(1L);

        when(institutionChnlPymMthdAccRepository.existsByInstitutionChannelPymMethodIdAndCurrency(requestDTO.getInstitutionChannelPymMethodId(), requestDTO.getCurrency()))
                .thenReturn(false);
        when(institutionChnlPymMethodService.getInstitutionChannelPymMethodById(requestDTO.getInstitutionChannelPymMethodId())).thenReturn(null);

        assertThrows(DataNotFoundException.class, () -> adminInstitutionChnlPymMthdAccService.createInstitutionChannelPymMethodAcc(requestDTO));
        verify(institutionChnlPymMethodService, times(1)).getInstitutionChannelPymMethodById(requestDTO.getInstitutionChannelPymMethodId());
    }

    @Test
    void createInstitutionChannelPymMethodAcc_shouldReturnInstitutionChnnlPymMthdAccDTO_whenSuccessful() throws MicroException {
        CreateInstitutionChnlPymMthdAccRequestDTO requestDTO = new CreateInstitutionChnlPymMthdAccRequestDTO();
        requestDTO.setInstitutionChannelPymMethodId(1L);
        requestDTO.setCurrency("USD");
        requestDTO.setCollectionAccountNo("123456");
        InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO = new InstitutionChannelPymMethodDTO();
        InstitutionChnnlPymMthdAccDTO institutionChnnlPymMthdAccDTO = new InstitutionChnnlPymMthdAccDTO();
        InstitutionChnnlPymMthdAcc institutionChnnlPymMthdAcc = new InstitutionChnnlPymMthdAcc();

        when(institutionChnnlPymMthdAccRepository.existsByInstitutionChannelPymMethodIdAndCurrency(requestDTO.getInstitutionChannelPymMethodId(), requestDTO.getCurrency()))
                .thenReturn(false);
        when(institutionChnlPymMethodService.getInstitutionChannelPymMethodById(requestDTO.getInstitutionChannelPymMethodId())).thenReturn(institutionChannelPymMethodDTO);
        when(institutionChnnlPymMthdAccMapper.toDTO(requestDTO)).thenReturn(institutionChnnlPymMthdAccDTO);
        when(institutionChnnlPymMthdAccMapper.toInstitutionChnnlPymMthdAcc(institutionChnnlPymMthdAccDTO)).thenReturn(institutionChnnlPymMthdAcc);
        when(institutionChnnlPymMthdAccRepository.save(institutionChnnlPymMthdAcc)).thenReturn(institutionChnnlPymMthdAcc);
        when(institutionChnnlPymMthdAccMapper.toDTO(institutionChnnlPymMthdAcc)).thenReturn(institutionChnnlPymMthdAccDTO);

        InstitutionChnnlPymMthdAccDTO result = adminInstitutionChnlPymMthdAccService.createInstitutionChannelPymMethodAcc(requestDTO);

        assertNotNull(result);
        assertEquals(institutionChnnlPymMthdAccDTO, result);
        verify(institutionChnnlPymMthdAccRepository, times(1)).existsByInstitutionChannelPymMethodIdAndCurrency(requestDTO.getInstitutionChannelPymMethodId(), requestDTO.getCurrency());
        verify(institutionChnlPymMethodService, times(1)).getInstitutionChannelPymMethodById(requestDTO.getInstitutionChannelPymMethodId());
        verify(institutionChnnlPymMthdAccMapper, times(1)).toDTO(requestDTO);
        verify(institutionChnnlPymMthdAccMapper, times(1)).toInstitutionChnnlPymMthdAcc(institutionChnnlPymMthdAccDTO);
        verify(institutionChnnlPymMthdAccRepository, times(1)).save(institutionChnnlPymMthdAcc);
        verify(institutionChnnlPymMthdAccMapper, times(1)).toDTO(institutionChnnlPymMthdAcc);
    }

    @Test
    void updateInstitutionChannelPymMethodAcc_shouldThrowDataNotFoundException_whenInstitutionChannelPymMethodAccDoesNotExist() {
        UpdateInstitutionChnlPymMthdAccRequestDTO requestDTO = new UpdateInstitutionChnlPymMthdAccRequestDTO();
        requestDTO.setId(1L);

        when(institutionChnnlPymMthdAccRepository.findById(requestDTO.getId())).thenReturn(Optional.empty());

        assertThrows(DataNotFoundException.class, () -> adminInstitutionChnlPymMthdAccService.updateInstitutionChannelPymMethodAcc(requestDTO));
        verify(institutionChnnlPymMthdAccRepository, times(1)).findById(requestDTO.getId());
    }

    @Test
    void updateInstitutionChannelPymMethodAcc_shouldReturnUpdatedInstitutionChnnlPymMthdAccDTO_whenSuccessful() throws MicroException {
        UpdateInstitutionChnlPymMthdAccRequestDTO requestDTO = new UpdateInstitutionChnlPymMthdAccRequestDTO();
        requestDTO.setId(1L);
        requestDTO.setInstitutionChannelPymMethodId(1L);
        requestDTO.setCurrency("USD");
        requestDTO.setCollectionAccountNo("123456");
        requestDTO.setExpenseAccountNo("EXP123");
        requestDTO.setInstitutionAccountNo("INST456");
        requestDTO.setExpenseType("EXPENSE");
        requestDTO.setIsActive(true);
        requestDTO.setUpdateUser("user");

        InstitutionChnnlPymMthdAcc existingInstitutionChnnlPymMthdAcc = new InstitutionChnnlPymMthdAcc();
        InstitutionChnnlPymMthdAccDTO existingInstitutionChnnlPymMthdAccDTO = new InstitutionChnnlPymMthdAccDTO();
        InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO = new InstitutionChannelPymMethodDTO();
        InstitutionChnnlPymMthdAcc updatedInstitutionChnnlPymMthdAcc = new InstitutionChnnlPymMthdAcc();
        InstitutionChnnlPymMthdAccDTO updatedInstitutionChnnlPymMthdAccDTO = new InstitutionChnnlPymMthdAccDTO();

        when(institutionChnnlPymMthdAccRepository.findById(requestDTO.getId())).thenReturn(Optional.of(existingInstitutionChnnlPymMthdAcc));
        when(institutionChnlPymMethodService.getInstitutionChannelPymMethodById(requestDTO.getInstitutionChannelPymMethodId())).thenReturn(institutionChannelPymMethodDTO);
        when(institutionChnnlPymMthdAccMapper.toDTO(existingInstitutionChnnlPymMthdAcc)).thenReturn(existingInstitutionChnnlPymMthdAccDTO);
        when(institutionChnnlPymMthdAccMapper.toInstitutionChnnlPymMthdAcc(existingInstitutionChnnlPymMthdAccDTO)).thenReturn(updatedInstitutionChnnlPymMthdAcc);
        when(institutionChnnlPymMthdAccRepository.save(updatedInstitutionChnnlPymMthdAcc)).thenReturn(updatedInstitutionChnnlPymMthdAcc);
        when(institutionChnnlPymMthdAccMapper.toDTO(updatedInstitutionChnnlPymMthdAcc)).thenReturn(updatedInstitutionChnnlPymMthdAccDTO);

        InstitutionChnnlPymMthdAccDTO result = adminInstitutionChnlPymMthdAccService.updateInstitutionChannelPymMethodAcc(requestDTO);

        assertNotNull(result);
        assertEquals(updatedInstitutionChnnlPymMthdAccDTO, result);
        verify(institutionChnnlPymMthdAccRepository, times(1)).findById(requestDTO.getId());
        verify(institutionChnlPymMethodService, times(1)).getInstitutionChannelPymMethodById(requestDTO.getInstitutionChannelPymMethodId());
        verify(institutionChnnlPymMthdAccMapper, times(1)).toDTO(existingInstitutionChnnlPymMthdAcc);
        verify(institutionChnnlPymMthdAccMapper, times(1)).toInstitutionChnnlPymMthdAcc(existingInstitutionChnnlPymMthdAccDTO);
        verify(institutionChnnlPymMthdAccRepository, times(1)).save(updatedInstitutionChnnlPymMthdAcc);
        verify(institutionChnnlPymMthdAccMapper, times(1)).toDTO(updatedInstitutionChnnlPymMthdAcc);
    }
}
