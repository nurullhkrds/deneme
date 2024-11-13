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
import com.ykb.payments.bill.transaction.institution.admin.service.impl.AdminInstitutionPymMethodServiceImpl;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionService;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminPaymentMethodService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionPymMethodRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionPymMethodRequestDTO;
import com.ykb.payments.bill.transaction.institution.domain.InstitutionPymMethod;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionPymMethodDTO;
import com.ykb.payments.bill.transaction.institution.dto.PaymentMethodDTO;
import com.ykb.payments.bill.transaction.institution.mapper.InstitutionPymMethodMapper;
import com.ykb.payments.bill.transaction.institution.repository.InstitutionPymMethodRepository;

import java.time.LocalDateTime;
import java.util.List;
import java.util.Optional;
import java.util.Arrays;

class AdminInstitutionPymMethodServiceImplTest {

    @Mock
    private InstitutionPymMethodRepository institutionPymMethodRepository;

    @Mock
    private InstitutionPymMethodMapper institutionPymMethodMapper;

    @Mock
    private AdminInstitutionService institutionService;

    @Mock
    private AdminPaymentMethodService paymentMethodService;

    @InjectMocks
    private AdminInstitutionPymMethodServiceImpl adminInstitutionPymMethodService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getAllInstitutionPymMethods_shouldReturnListOfInstitutionPymMethodDTO() {
        List<InstitutionPymMethod> institutionPymMethodList = Arrays.asList(new InstitutionPymMethod(), new InstitutionPymMethod());
        List<InstitutionPymMethodDTO> expectedDTOs = Arrays.asList(new InstitutionPymMethodDTO(), new InstitutionPymMethodDTO());

        when(institutionPymMethodRepository.findAll()).thenReturn(institutionPymMethodList);
        when(institutionPymMethodMapper.toDTOList(institutionPymMethodList)).thenReturn(expectedDTOs);

        List<InstitutionPymMethodDTO> result = adminInstitutionPymMethodService.getAllInstitutionPymMethods();

        assertNotNull(result);
        assertEquals(2, result.size());
        verify(institutionPymMethodRepository, times(1)).findAll();
        verify(institutionPymMethodMapper, times(1)).toDTOList(institutionPymMethodList);
    }

    @Test
    void getInstitutionPymMethodById_shouldReturnInstitutionPymMethodDTO_whenFound() {
        Long id = 1L;
        InstitutionPymMethod institutionPymMethod = new InstitutionPymMethod();
        InstitutionPymMethodDTO expectedDTO = new InstitutionPymMethodDTO();

        when(institutionPymMethodRepository.findById(id)).thenReturn(Optional.of(institutionPymMethod));
        when(institutionPymMethodMapper.toDTO(institutionPymMethod)).thenReturn(expectedDTO);

        InstitutionPymMethodDTO result = adminInstitutionPymMethodService.getInstitutionPymMethodById(id);

        assertNotNull(result);
        assertEquals(expectedDTO, result);
        verify(institutionPymMethodRepository, times(1)).findById(id);
        verify(institutionPymMethodMapper, times(1)).toDTO(institutionPymMethod);
    }

    @Test
    void getInstitutionPymMethodById_shouldReturnNull_whenNotFound() {
        Long id = 1L;

        when(institutionPymMethodRepository.findById(id)).thenReturn(Optional.empty());

        InstitutionPymMethodDTO result = adminInstitutionPymMethodService.getInstitutionPymMethodById(id);

        assertNull(result);
        verify(institutionPymMethodRepository, times(1)).findById(id);
    }

    @Test
    void createInstitutionPymMethod_shouldThrowDataConflictException_whenInstitutionPymMethodAlreadyExists() {
        CreateInstitutionPymMethodRequestDTO requestDTO = new CreateInstitutionPymMethodRequestDTO();
        requestDTO.setInstitutionId(1L);
        requestDTO.setPaymentMethod(() -> "PAYMENT_METHOD");

        when(institutionPymMethodRepository.findByInstitutionIdAndPymMethod(requestDTO.getInstitutionId(), requestDTO.getPaymentMethod().getValue()))
                .thenReturn(Optional.of(new InstitutionPymMethod()));

        assertThrows(DataConflictException.class, () -> adminInstitutionPymMethodService.createInstitutionPymMethod(requestDTO));
        verify(institutionPymMethodRepository, times(1)).findByInstitutionIdAndPymMethod(requestDTO.getInstitutionId(), requestDTO.getPaymentMethod().getValue());
    }

    @Test
    void createInstitutionPymMethod_shouldThrowDataNotFoundException_whenInstitutionNotFound() throws MicroException {
        CreateInstitutionPymMethodRequestDTO requestDTO = new CreateInstitutionPymMethodRequestDTO();
        requestDTO.setInstitutionId(1L);

        when(institutionPymMethodRepository.findByInstitutionIdAndPymMethod(requestDTO.getInstitutionId(), requestDTO.getPaymentMethod().getValue()))
                .thenReturn(Optional.empty());
        when(institutionService.getInstitutionByIdTypeSecond(requestDTO.getInstitutionId())).thenReturn(null);

        assertThrows(DataNotFoundException.class, () -> adminInstitutionPymMethodService.createInstitutionPymMethod(requestDTO));
        verify(institutionService, times(1)).getInstitutionByIdTypeSecond(requestDTO.getInstitutionId());
    }

    @Test
    void createInstitutionPymMethod_shouldReturnInstitutionPymMethodDTO_whenSuccessful() throws MicroException {
        CreateInstitutionPymMethodRequestDTO requestDTO = new CreateInstitutionPymMethodRequestDTO();
        requestDTO.setInstitutionId(1L);
        requestDTO.setPaymentMethod(() -> "PAYMENT_METHOD");
        InstitutionDTO institutionDTO = new InstitutionDTO();
        PaymentMethodDTO paymentMethodDTO = new PaymentMethodDTO();
        InstitutionPymMethodDTO institutionPymMethodDTO = new InstitutionPymMethodDTO();
        InstitutionPymMethod institutionPymMethod = new InstitutionPymMethod();

        when(institutionPymMethodRepository.findByInstitutionIdAndPymMethod(requestDTO.getInstitutionId(), requestDTO.getPaymentMethod().getValue()))
                .thenReturn(Optional.empty());
        when(institutionService.getInstitutionByIdTypeSecond(requestDTO.getInstitutionId())).thenReturn(institutionDTO);
        when(paymentMethodService.getPaymentMethodByMethod(requestDTO.getPaymentMethod())).thenReturn(paymentMethodDTO);
        when(institutionPymMethodMapper.toDTO(requestDTO)).thenReturn(institutionPymMethodDTO);
        when(institutionPymMethodMapper.toInstitutionPymMethod(institutionPymMethodDTO)).thenReturn(institutionPymMethod);
        when(institutionPymMethodRepository.save(institutionPymMethod)).thenReturn(institutionPymMethod);
        when(institutionPymMethodMapper.toDTO(institutionPymMethod)).thenReturn(institutionPymMethodDTO);

        InstitutionPymMethodDTO result = adminInstitutionPymMethodService.createInstitutionPymMethod(requestDTO);

        assertNotNull(result);
        assertEquals(institutionPymMethodDTO, result);
        verify(institutionPymMethodRepository, times(1)).findByInstitutionIdAndPymMethod(requestDTO.getInstitutionId(), requestDTO.getPaymentMethod().getValue());
        verify(institutionService, times(1)).getInstitutionByIdTypeSecond(requestDTO.getInstitutionId());
        verify(paymentMethodService, times(1)).getPaymentMethodByMethod(requestDTO.getPaymentMethod());
        verify(institutionPymMethodMapper, times(1)).toDTO(requestDTO);
        verify(institutionPymMethodMapper, times(1)).toInstitutionPymMethod(institutionPymMethodDTO);
        verify(institutionPymMethodRepository, times(1)).save(institutionPymMethod);
        verify(institutionPymMethodMapper, times(1)).toDTO(institutionPymMethod);
    }

    @Test
    void updateInstitutionPymMethod_shouldThrowDataNotFoundException_whenInstitutionPymMethodNotFound() {
        UpdateInstitutionPymMethodRequestDTO requestDTO = new UpdateInstitutionPymMethodRequestDTO();
        requestDTO.setId(1L);

        when(institutionPymMethodRepository.findById(requestDTO.getId())).thenReturn(Optional.empty());

        assertThrows(DataNotFoundException.class, () -> adminInstitutionPymMethodService.updateInstitutionPymMethod(requestDTO));
        verify(institutionPymMethodRepository, times(1)).findById(requestDTO.getId());
    }

    @Test
    void updateInstitutionPymMethod_shouldReturnUpdatedInstitutionPymMethodDTO_whenSuccessful() throws MicroException {
        UpdateInstitutionPymMethodRequestDTO requestDTO = new UpdateInstitutionPymMethodRequestDTO();
        requestDTO.setId(1L);
        requestDTO.setInstitutionId(1L);
        requestDTO.setPaymentMethod(() -> "PAYMENT_METHOD");
        requestDTO.setExpenseCode("EXPENSE_CODE");
        requestDTO.setIsActive(true);
        requestDTO.setUpdateUser("user");

        InstitutionPymMethod existingInstitutionPymMethod = new InstitutionPymMethod();
        InstitutionPymMethodDTO existingInstitutionPymMethodDTO = new InstitutionPymMethodDTO();
        InstitutionDTO institutionDTO = new InstitutionDTO();
        PaymentMethodDTO paymentMethodDTO = new PaymentMethodDTO();
        InstitutionPymMethod updatedInstitutionPymMethod = new InstitutionPymMethod();
        InstitutionPymMethodDTO updated
