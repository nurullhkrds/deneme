import com.fasterxml.jackson.databind.ObjectMapper;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertNull;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.*;

public class PaymentCommissionServiceImplTest {

    @Mock
    private InstitutionPaymentMethodService instPaymentMethodService;

    @Mock
    private InstitutionDebtTypeService institutionDebtTypeService;

    @Mock
    private CommissionService commissionService;

    @Mock
    private ProvisionService provisionService;

    @InjectMocks
    private PaymentCommissionServiceImpl paymentCommissionServiceImpl;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    public void testPerformCommission_Success() throws Exception {
        // Prepare mock objects
        ProvisionDTO provisionDTO = new ProvisionDTO();
        provisionDTO.setInstitutionDebtTypeId("debtTypeId");
        provisionDTO.setCustomerNo("customerNo");

        InstitutionDebtTypeDTO debtTypeDTO = new InstitutionDebtTypeDTO();
        debtTypeDTO.setInstitution(new InstitutionDTO());
        debtTypeDTO.getInstitution().setProduct(new ProductDTO());
        debtTypeDTO.getInstitution().getProduct().setCode("productCode");
        debtTypeDTO.getInstitution().setInstitutionCode("institutionCode");

        InstitutionPymMethodWebDTO pymMethodDTO = new InstitutionPymMethodWebDTO();
        pymMethodDTO.setExpenseCode("expenseCode");
        pymMethodDTO.setExpenseAccountNo("accountNo");
        pymMethodDTO.setExpenseType(EnumExpenseType.FROM_CUSTOMER.getValue());

        ResponseCommissionInformation responseCommissionInformation = new ResponseCommissionInformation();
        // Mock the behavior of services
        when(provisionService.getProvisionRecord(any())).thenReturn(provisionDTO);
        when(institutionDebtTypeService.getDebtType(any())).thenReturn(debtTypeDTO);
        when(instPaymentMethodService.getInstitutionExpenseCode(any(), any(), any(), any(), any())).thenReturn(pymMethodDTO);
        when(commissionService.inquireCommission(any())).thenReturn(responseCommissionInformation);

        // Prepare request DTO
        CommissionServiceRequestDTO requestDTO = new CommissionServiceRequestDTO();
        requestDTO.setBillProvisionId("billProvisionId");
        requestDTO.setPaymentMethod(EnumPaymentMethod.ACCOUNT.getValue());
        requestDTO.setPaymentAmount(100.0);
        requestDTO.setPaymentCurrency("TRY");
        requestDTO.setChannelCode("channelCode");
        requestDTO.setAccountBranchCode("branchCode");

        // Call the method under test
        ResponseCommissionInformation result = paymentCommissionServiceImpl.performCommission(requestDTO);

        // Verify the result
        assertEquals(responseCommissionInformation, result);
        verify(provisionService).updateCommissionData(anyString(), anyString());
    }

    @Test
    public void testPerformCommission_NoInstitutionPaymentMethod() throws Exception {
        // Prepare mock objects
        ProvisionDTO provisionDTO = new ProvisionDTO();
        provisionDTO.setInstitutionDebtTypeId("debtTypeId");

        InstitutionDebtTypeDTO debtTypeDTO = new InstitutionDebtTypeDTO();
        debtTypeDTO.setInstitution(new InstitutionDTO());
        debtTypeDTO.getInstitution().setProduct(new ProductDTO());
        debtTypeDTO.getInstitution().getProduct().setCode("productCode");
        debtTypeDTO.getInstitution().setInstitutionCode("institutionCode");

        // Mock the behavior of services
        when(provisionService.getProvisionRecord(any())).thenReturn(provisionDTO);
        when(institutionDebtTypeService.getDebtType(any())).thenReturn(debtTypeDTO);
        when(instPaymentMethodService.getInstitutionExpenseCode(any(), any(), any(), any(), any())).thenReturn(null);

        // Prepare request DTO
        CommissionServiceRequestDTO requestDTO = new CommissionServiceRequestDTO();
        requestDTO.setBillProvisionId("billProvisionId");
        requestDTO.setPaymentMethod(EnumPaymentMethod.ACCOUNT.getValue());

        // Call the method under test
        ResponseCommissionInformation result = paymentCommissionServiceImpl.performCommission(requestDTO);

        // Verify the result
        assertNull(result);
    }

    @Test
    public void testPerformCommission_Exception() throws Exception {
        // Mock the behavior of services to throw an exception
        when(provisionService.getProvisionRecord(any())).thenThrow(new RuntimeException());

        // Prepare request DTO
        CommissionServiceRequestDTO requestDTO = new CommissionServiceRequestDTO();
        requestDTO.setBillProvisionId("billProvisionId");

        // Call the method under test and expect an exception
        try {
            paymentCommissionServiceImpl.performCommission(requestDTO);
        } catch (BillException e) {
            assertEquals(EnumBillResult.BILL_EXPENSE_INQUIRY_ERROR, e.getBillResult());
        }
    }
}
