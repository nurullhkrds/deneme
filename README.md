import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import com.fasterxml.jackson.databind.ObjectMapper;

@ExtendWith(MockitoExtension.class)
public class PaymentAdkControllerTest {

    private MockMvc mockMvc;

    @Mock
    private PaymentService paymentService;

    @Mock
    private PaymentFacade paymentFacade;

    @Mock
    private InstitutionBarcodeService institutionBarcodeService;

    @Mock
    private RequestContext requestContext;

    @InjectMocks
    private PaymentAdkController paymentAdkController;

    private ObjectMapper objectMapper;

    @BeforeEach
    public void setup() {
        objectMapper = new ObjectMapper();
        mockMvc = MockMvcBuilders.standaloneSetup(paymentAdkController).build();
    }

    @Test
    public void testGetBillPaymentExpense() throws Exception {
        GetBillPaymentExpenseRequestDTO requestDTO = new GetBillPaymentExpenseRequestDTO();
        requestDTO.setAgentCode("agentCode");
        requestDTO.setChannelCode("channelCode");
        requestDTO.setOperatingBranchCode("branchCode");

        GetBillPaymentExpenseResponseDTO responseDTO = new GetBillPaymentExpenseResponseDTO();
        // Populate responseDTO with expected data

        when(paymentFacade.getBillPaymentExpense(any(GetBillPaymentExpenseRequestDTO.class))).thenReturn(responseDTO);

        mockMvc.perform(get("/adkBillPayment/getBillPaymentExpense")
                .param("agentCode", requestDTO.getAgentCode())
                .param("channelCode", requestDTO.getChannelCode())
                .param("operatingBranchCode", requestDTO.getOperatingBranchCode())
                .header("x-trace-id", "traceId")
                .header("x-session-id", "sessionId")
                .contentType(MediaType.APPLICATION_JSON))
                .andExpect(status().isOk())
                .andExpect(content().contentType(MediaType.APPLICATION_JSON))
                .andExpect(jsonPath("$.field").value("expectedValue"));

        verify(paymentFacade, times(1)).getBillPaymentExpense(any(GetBillPaymentExpenseRequestDTO.class));
    }
}
