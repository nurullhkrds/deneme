import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mapstruct.factory.Mappers;
import org.springframework.util.CollectionUtils;

import java.util.Collections;

public class ProcessExecutionMapperTest {

    private ProcessExecutionMapper mapper;

    @BeforeEach
    public void setUp() {
        mapper = Mappers.getMapper(ProcessExecutionMapper.class);
    }

    @Test
    public void testToQueryBillProcessInput() {
        QueryBillsRequest request = new QueryBillsRequest();
        request.setOperatingBranchCode("001");
        request.setDebtTypeID(1);

        QueryBillProcessInput input = mapper.toQueryBillProcessInput(request);

        assertEquals("001", input.getBranchCode());
        assertEquals(1, input.getInstitutionDebtTypeId());
    }

    @Test
    public void testToQueryBillsResponse() {
        QueryBillsProcessOutput output = new QueryBillsProcessOutput();
        output.setProvisionDTOList(Collections.emptyList());

        QueryBillsResponse response = mapper.toQueryBillsResponse(output);

        assertNotNull(response);
    }

    @Test
    public void testAfterToGetQueryBillsResponse() {
        QueryBillsProcessOutput output = mock(QueryBillsProcessOutput.class);
        ProvisionDTO provisionDTO = mock(ProvisionDTO.class);
        when(output.getProvisionDTOList()).thenReturn(Collections.singletonList(provisionDTO));
        when(provisionDTO.getSubscriberName()).thenReturn("John Doe");
        when(provisionDTO.getBillNo()).thenReturn("123456");
        when(provisionDTO.getAmount()).thenReturn(100.0);
        when(provisionDTO.getBillDueDate()).thenReturn("2023-01-01");
        when(provisionDTO.getCurrency().getValue()).thenReturn("USD");
        when(provisionDTO.getBillTerm()).thenReturn("Monthly");
        when(provisionDTO.getId().toString()).thenReturn("1");
        when(provisionDTO.getExplanation()).thenReturn("Explanation");
        when(provisionDTO.getIsPayable()).thenReturn(true);

        QueryBillsResponse response = new QueryBillsResponse();

        mapper.afterToGetQueryBillsResponse(response, output);

        assertEquals("John Doe", response.getSubscriberName());
        assertEquals(1, response.getBillList().size());
        QueriedBillResponseWebDTO bill = response.getBillList().get(0);
        assertEquals("123456", bill.getBillNo());
        assertEquals(100.0, bill.getBillAmount());
        assertEquals("2023-01-01", bill.getBillDueDate());
        assertEquals("USD", bill.getCurrency());
        assertEquals("Monthly", bill.getBillTerm());
        assertEquals("1", bill.getBillProvisionId());
        assertEquals("Explanation", bill.getExplanation());
        assertTrue(bill.getPayable());
    }

    @Test
    public void testToBillPaymentProcessInput() {
        DoBillPaymentRequest request = new DoBillPaymentRequest();
        request.setOperatingBranchCode("002");

        BillPaymentProcessInput input = mapper.toBillPaymentProcessInput(request);

        assertEquals("002", input.getBranchCode());
    }

    @Test
    public void testToBillPaymentReverseProcessInput() {
        CancelBillPaymentRequest request = new CancelBillPaymentRequest();
        request.setOperatingBranchCode("003");

        BillPaymentReverseProcessInput input = mapper.toBillPaymentReverseProcessInput(request);

        assertEquals("003", input.getBranchCode());
    }
}
