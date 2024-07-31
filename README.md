import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mapstruct.factory.Mappers;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import java.util.Arrays;
import java.util.Collections;
import java.util.List;

public class MicroHarmoniMapperTest {

    @InjectMocks
    private MicroHarmoniMapperImpl mapper = Mappers.getMapper(MicroHarmoniMapperImpl.class);

    @Mock
    private ChannelUtil channelUtil;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    public void testToRequestQueryBillsHmn() {
        QueryBillsRequest request = new QueryBillsRequest();
        request.setCurrency("TRY");
        request.setSubscriberNoPartList(Arrays.asList(new SubscriberNoPartRequestWebDTO("123")));

        RequestQueryBillsHmn result = mapper.toRequestQueryBillsHmn(request);

        assertNotNull(result);
        assertEquals(EnumCurrencyCode.TURKISH_LIRA_YTL.getValue(), result.getCurrencyCode());
        assertEquals("123", result.getSubscriberNo1());
        assertNull(result.getSubscriberNo2());
        assertNull(result.getSubscriberNo3());
    }

    @Test
    public void testToQueryBillsResponse() {
        ResponseQueryBillsHmn responseQueryBillsMicro = new ResponseQueryBillsHmn();
        HmnQueriedBills bill = new HmnQueriedBills();
        bill.setSubscriberNo("12345");
        bill.setSubscriberName("John Doe");
        bill.setBillNo("67890");
        bill.setBillTerm("202107");
        bill.setBillAmount("100.00");
        bill.setBankReferenceNo("ref123");
        bill.setPayable(EnumYesNo.YES.getValue());
        bill.setBillDueDate("2021-08-01");
        bill.setCurrency("TRY");
        responseQueryBillsMicro.setBillList(Collections.singletonList(bill));

        QueryBillsRequest queryBillsRequest = new QueryBillsRequest();
        queryBillsRequest.setProductCode("PROD001");
        queryBillsRequest.setInstitutionCode("INST001");
        queryBillsRequest.setDebtTypeID("DEBT001");

        QueryBillsResponse result = mapper.toQueryBillsResponse(responseQueryBillsMicro, queryBillsRequest);

        assertNotNull(result);
        assertEquals("12345", result.getSubscriberNo());
        assertEquals("John Doe", result.getSubscriberName());
        assertEquals(1, result.getBillList().size());
        assertEquals("67890", result.getBillList().get(0).getBillNo());
    }

    @Test
    public void testToRequestBillPaymentExpenseHmn() {
        GetBillPaymentExpenseRequestDTO requestDTO = new GetBillPaymentExpenseRequestDTO();
        // populate requestDTO as needed

        RequestBillPaymentExpenseHmn result = mapper.toRequestBillPaymentExpenseHmn(requestDTO);

        assertNotNull(result);
        // perform additional assertions as needed
    }

    @Test
    public void testToGetBillPaymentExpenseResponseDTO() {
        ResponseBillPaymentExpenseHmn responseHmn = new ResponseBillPaymentExpenseHmn();
        // populate responseHmn as needed

        GetBillPaymentExpenseResponseDTO result = mapper.toGetBillPaymentExpenseResponseDTO(responseHmn);

        assertNotNull(result);
        // perform additional assertions as needed
    }

    @Test
    public void testToRequestPayBillHmn() {
        DoBillPaymentRequest request = new DoBillPaymentRequest();
        request.setChannelCode("WEB");

        when(channelUtil.convertToHarmoniChannel(anyString())).thenReturn("HARMONI_WEB");

        RequestPayBillHmn result = mapper.toRRequestPayBillHmn(request);

        assertNotNull(result);
        assertEquals("HARMONI_WEB", result.getChannelCode());
    }

    @Test
    public void testToResponsePayBillHmn() {
        ResponsePayBillHmn responseHmn = new ResponsePayBillHmn();
        // populate responseHmn as needed

        DoBillPaymentResponse result = mapper.toResponsePayBillHmn(responseHmn);

        assertNotNull(result);
        // perform additional assertions as needed
    }
}
