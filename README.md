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
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionChnlPymMethodMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionChannelService;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminPaymentMethodService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionChnlPymMethodRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionChnlPymMethodRequestDTO;
import com.ykb.payments.bill.transaction.institution.domain.InstitutionChannelPymMethod;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionChannelDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionChannelPymMethodDTO;
import com.ykb.payments.bill.transaction.institution.dto.PaymentMethodDTO;
import com.ykb.payments.bill.transaction.institution.repository.InstitutionChannelPymMethodRepository;

import java.time.LocalDateTime;
import java.util.List;
import java.util.Optional;
import java.util.Arrays;

class AdminInstitutionChnlPymMethodServiceImplTest {

    @Mock
    private InstitutionChannelPymMethodRepository institutionChannelPymMethodRepository;

    @Mock
    private AdminInstitutionChnlPymMethodMapper institutionChnlPymMethodMapper;

    @Mock
    private AdminInstitutionChannelService institutionChannelService;

    @Mock
    private AdminPaymentMethodService paymentMethodService;

    @InjectMocks
    private AdminInstitutionChnlPymMethodServiceImpl adminInstitutionChnlPymMethodService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getAll_shouldReturnListOfInstitutionChannelPymMethodDTO() {
        List<InstitutionChannelPymMethod> institutionChannelPymMethodList = Arrays.asList(new InstitutionChannelPymMethod(), new InstitutionChannelPymMethod());
        List<InstitutionChannelPymMethodDTO> expectedDTOs = Arrays.asList(new InstitutionChannelPymMethodDTO(), new InstitutionChannelPymMethodDTO());

        when(institutionChannelPymMethodRepository.findAll()).thenReturn(institutionChannelPymMethodList);
        when(institutionChnlPymMethodMapper.toDTOList(institutionChannelPymMethodList)).thenReturn(expectedDTOs);

        List<InstitutionChannelPymMethodDTO> result = adminInstitutionChnlPymMethodService.getAll();

        assertNotNull(result);
        assertEquals(2, result.size());
        verify(institutionChannelPymMethodRepository, times(1)).findAll();
        verify(institutionChnlPymMethodMapper, times(1)).toDTOList(institutionChannelPymMethodList);
    }

    @Test
    void getInstitutionChannelPymMethodById_shouldReturnInstitutionChannelPymMethodDTO_whenFound() {
        Long id = 1L;
        InstitutionChannelPymMethod institutionChannelPymMethod = new InstitutionChannelPymMethod();
        InstitutionChannelPymMethodDTO expectedDTO = new InstitutionChannelPymMethodDTO();

        when(institutionChannelPymMethodRepository.findById(id)).thenReturn(Optional.of(institutionChannelPymMethod));
        when(institutionChnlPymMethodMapper.toDTO(institutionChannelPymMethod)).thenReturn(expectedDTO);

        InstitutionChannelPymMethodDTO result = adminInstitutionChnlPymMethodService.getInstitutionChannelPymMethodById(id);

        assertNotNull(result);
        assertEquals(expectedDTO, result);
        verify(institutionChannelPymMethodRepository, times(1)).findById(id);
        verify(institutionChnlPymMethodMapper, times(1)).toDTO(institutionChannelPymMethod);
    }

    @Test
    void getInstitutionChannelPymMethodById_shouldReturnNull_whenNotFound() {
        Long id = 1L;

        when(institutionChannelPymMethodRepository.findById(id)).thenReturn(Optional.empty());

        InstitutionChannelPymMethodDTO result = adminInstitutionChnlPymMethodService.getInstitutionChannelPymMethodById(id);

        assertNull(result);
        verify(institutionChannelPymMethodRepository, times(1)).findById(id);
    }

    @Test
    void createInstitutionChannelPymMethod_shouldThrowDataConflictException_whenInstitutionChannelPymMethodAlreadyExists() {
        CreateInstitutionChnlPymMethodRequestDTO requestDTO = new CreateInstitutionChnlPymMethodRequestDTO();
        requestDTO.setInstitutionChannelId(1L);
        requestDTO.setPaymentMethod("PAYMENT_METHOD");

        when(institutionChannelPymMethodRepository.findFirstByChannelIdAndPaymentMethod(requestDTO.getInstitutionChannelId(), requestDTO.getPaymentMethod()))
                .thenReturn(new InstitutionChannelPymMethod());

        assertThrows(DataConflictException.class, () -> adminInstitutionChnlPymMethodService.createInstitutionChannelPymMethod(requestDTO));
        verify(institutionChannelPymMethodRepository, times(1)).findFirstByChannelIdAndPaymentMethod(requestDTO.getInstitutionChannelId(), requestDTO.getPaymentMethod());
    }

    @Test
    void createInstitutionChannelPymMethod_shouldThrowDataNotFoundException_whenInstitutionChannelNotFound() throws MicroException {
        CreateInstitutionChnlPymMethodRequestDTO requestDTO = new CreateInstitutionChnlPymMethodRequestDTO();
        requestDTO.setInstitutionChannelId(1L);
        requestDTO.setPaymentMethod("PAYMENT_METHOD");

        when(institutionChannelPymMethodRepository.findFirstByChannelIdAndPaymentMethod(requestDTO.getInstitutionChannelId(), requestDTO.getPaymentMethod()))
                .thenReturn(null);
        when(institutionChannelService.getInstitutionChannelById(requestDTO.getInstitutionChannelId())).thenReturn(null);

        assertThrows(DataNotFoundException.class, () -> adminInstitutionChnlPymMethodService.createInstitutionChannelPymMethod(requestDTO));
        verify(institutionChannelService, times(1)).getInstitutionChannelById(requestDTO.getInstitutionChannelId());
    }

    @Test
    void createInstitutionChannelPymMethod_shouldReturnInstitutionChannelPymMethodDTO_whenSuccessful() throws MicroException {
        CreateInstitutionChnlPymMethodRequestDTO requestDTO = new CreateInstitutionChnlPymMethodRequestDTO();
        requestDTO.setInstitutionChannelId(1L);
        requestDTO.setPaymentMethod("PAYMENT_METHOD");
        InstitutionChannelDTO institutionChannelDTO = new InstitutionChannelDTO();
        PaymentMethodDTO paymentMethodDTO = new PaymentMethodDTO();
        InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO = new InstitutionChannelPymMethodDTO();
        InstitutionChannelPymMethod institutionChannelPymMethod = new InstitutionChannelPymMethod();

        when(institutionChannelPymMethodRepository.findFirstByChannelIdAndPaymentMethod(requestDTO.getInstitutionChannelId(), requestDTO.getPaymentMethod()))
                .thenReturn(null);
        when(institutionChannelService.getInstitutionChannelById(requestDTO.getInstitutionChannelId())).thenReturn(institutionChannelDTO);
        when(paymentMethodService.getPaymentMethodByMethod(requestDTO.getPaymentMethod())).thenReturn(paymentMethodDTO);
        when(institutionChnlPymMethodMapper.toDTO(requestDTO)).thenReturn(institutionChannelPymMethodDTO);
        when(institutionChnlPymMethodMapper.toInstitutionChannelPymMethod(institutionChannelPymMethodDTO)).thenReturn(institutionChannelPymMethod);
        when(institutionChannelPymMethodRepository.save(institutionChannelPymMethod)).thenReturn(institutionChannelPymMethod);
        when(institutionChnlPymMethodMapper.toDTO(institutionChannelPymMethod)).thenReturn(institutionChannelPymMethodDTO);

        InstitutionChannelPymMethodDTO result = adminInstitutionChnlPymMethodService.createInstitutionChannelPymMethod(requestDTO);

        assertNotNull(result);
        assertEquals(institutionChannelPymMethodDTO, result);
        verify(institutionChannelPymMethodRepository, times(1)).findFirstByChannelIdAndPaymentMethod(requestDTO.getInstitutionChannelId(), requestDTO.getPaymentMethod());
        verify(institutionChannelService, times(1)).getInstitutionChannelById(requestDTO.getInstitutionChannelId());
        verify(paymentMethodService, times(1)).getPaymentMethodByMethod(requestDTO.getPaymentMethod());
        verify(institutionChnlPymMethodMapper, times(1)).toDTO(requestDTO);
        verify(institutionChnlPymMethodMapper, times(1)).toInstitutionChannelPymMethod(institutionChannelPymMethodDTO);
        verify(institutionChannelPymMethodRepository, times(1)).save(institutionChannelPymMethod);
        verify(institutionChnlPymMethodMapper, times(1)).toDTO(institutionChannelPymMethod);
    }

    @Test
    void updateInstitutionChannelPymMethod_shouldThrowDataNotFoundException_whenInstitutionChannelPymMethodDoesNotExist() {
        UpdateInstitutionChnlPymMethodRequestDTO requestDTO = new UpdateInstitutionChnlPymMethodRequestDTO();
        requestDTO.setId(1L);

        when(institutionChannelPymMethodRepository.findById(requestDTO.getId())).thenReturn(Optional.empty());

        assertThrows(DataNotFoundException.class, () -> adminInstitutionChnlPymMethodService.updateInstitutionChannelPymMethod(requestDTO));
        verify(institutionChannelPymMethodRepository, times(1)).findById(requestDTO.getId());
    }

    @Test
    void updateInstitutionChannelPymMethod_shouldReturnUpdatedInstitutionChannelPymMethodDTO_whenSuccessful() throws MicroException {
        UpdateInstitutionChnlPymMethodRequestDTO requestDTO = new UpdateInstitutionChnlPymMethodRequestDTO();
        requestDTO.setId(1L);
        requestDTO.setInstitutionChannelId(1L);
        requestDTO.setPaymentMethod("PAYMENT_METHOD");
        requestDTO.setIsActive(true);
        requestDTO.setUpdateUser("user");
        requestDTO.setBlockDayCount(5);
        requestDTO.setBlockDayType("DAY_TYPE");
        requestDTO.setProfitShareRate(10.0);
        requestDTO.setAccountingTemplateCode("ACCOUNT_CODE");
        requestDTO.setBlockDayStrategyCode("BLOCK_STRATEGY_CODE");

        InstitutionChannelPymMethod existingInstitutionChannelPymMethod = new InstitutionChannelPymMethod();
        InstitutionChannelPymMethodDTO existingInstitutionChannelPymMethodDTO = new InstitutionChannelPymMethodDTO();
        InstitutionChannelDTO institutionChannelDTO = new InstitutionChannelDTO();
        PaymentMethodDTO paymentMethodDTO = new PaymentMethodDTO();
        InstitutionChannelPymMethod updatedInstitutionChannelPymMethod = new InstitutionChannelPymMethod();
        InstitutionChannelPymMethodDTO updatedInstitutionChannelPymMethodDTO = new InstitutionChannelPymMethodDTO();

        when(institutionChannelPymMethodRepository.findById(requestDTO.getId())).thenReturn(Optional.of(existingInstitutionChannelPymMethod));
        when(institutionChannelService.getInstitutionChannelById(requestDTO.getInstitutionChannelId())).thenReturn(institutionChannelDTO);
        when(paymentMethodService.getPaymentMethodByMethod(requestDTO.getPaymentMethod())).thenReturn(paymentMethodDTO);
        when(institutionChnlPymMethodMapper.toDTO(existingInstitutionChannelPymMethod)).thenReturn(existingInstitutionChannelPymMethodDTO);
        when(institutionChnlPymMethodMapper.toInstitutionChannelPymMethod(existingInstitutionChannelPymMethodDTO)).thenReturn(updatedInstitutionChannelPymMethod);
        when(institutionChannelPymMethodRepository.save(updatedInstitutionChannelPymMethod)).thenReturn(updatedInstitutionChannelPymMethod);
        when(institutionChnlPymMethodMapper.toDTO(updatedInstitutionChannelPymMethod)).thenReturn(updatedInstitutionChannelPymMethodDTO);

        InstitutionChannelPymMethodDTO result = adminInstitutionChnlPymMethodService.updateInstitutionChannelPymMethod(requestDTO);

        assertNotNull(result);
        assertEquals(updatedInstitutionChannelPymMethodDTO, result);
        verify(institutionChannelPymMethodRepository, times(1)).findById(requestDTO.getId());
        verify(institutionChannelService, times(1)).getInstitutionChannelById(requestDTO.getInstitutionChannelId());
        verify(paymentMethodService, times(1)).getPaymentMethodByMethod(requestDTO.getPaymentMethod());
        verify(institutionChnlPymMethodMapper, times(1)).toDTO(existingInstitutionChannelPymMethod);
        verify(institutionChnlPymMethodMapper, times(1)).toInstitutionChannelPymMethod(existingInstitutionChannelPymMethodDTO);
        verify(institutionChannelPymMethodRepository, times(1)).save(updatedInstitutionChannelPymMethod);
        verify(institutionChnlPymMethodMapper, times(1)).toDTO(updatedInstitutionChannelPymMethod);
    }
}
