import com.ykb.payments.bill.transaction.common.constant.BillTransactionConstant;
import com.ykb.payments.bill.transaction.external.harmoni.billpayment.rest.response.*;
import com.ykb.payments.bill.transaction.institution.enums.EnumPaymentMethod;
import com.ykb.payments.bill.transaction.payment.mapper.HarmoniMicroMapper;
import com.ykb.payments.bill.transaction.payment.web.request.*;
import com.ykb.payments.bill.transaction.payment.web.response.*;
import com.ykb.payments.bill.transaction.subscriber.model.GetBillPaymentExpenseRequestDTO;
import com.ykb.payments.bill.transaction.subscriber.model.GetBillPaymentExpenseResponseDTO;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mapstruct.factory.Mappers;
import org.mockito.InjectMocks;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.util.StringUtils;

import java.util.List;

import static org.junit.jupiter.api.Assertions.*;

@ExtendWith(MockitoExtension.class)
public class HarmoniMicroMapperTest {

    @InjectMocks
    private HarmoniMicroMapper mapper = Mappers.getMapper(HarmoniMicroMapper.class);

    @Test
    public void testToQueryBillRequest() {
        RequestHarmoniQueryBills request = new RequestHarmoniQueryBills();
        request.setAgentCode("agent");
        request.setChannelCode("channel");
        request.setOperatingBranchCode("branch");
        request.setInstitution("institution");
        request.setProduct("product");
        request.setSubscriberNo1("subNo1");
        request.setIdentityNo("12345678901");

        QueryBillsRequest result = mapper.toQueryBillRequest(request);

        assertEquals("agent", result.getAgentCode());
        assertEquals("channel", result.getChannelCode());
        assertEquals("branch", result.getOperatingBranchCode());
        assertEquals("institution", result.getInstitutionCode());
        assertEquals("product", result.getProductCode());
        assertEquals("subNo1", result.getSubscriberNo());
        assertEquals(Long.valueOf(12345678901L), result.getIdentityNo());
    }

    @Test
    public void testExtractIdentityNo() {
        RequestHarmoniQueryBills request = new RequestHarmoniQueryBills();
        request.setIdentityNo("12345678901");

        Long result = mapper.extractIdentityNo(request);

        assertEquals(Long.valueOf(12345678901L), result);
    }

    @Test
    public void testToResponseHarmoniQueryBills() {
        QueryBillsResponse queryBillsResponse = new QueryBillsResponse();
        RequestHarmoniQueryBills harmoniRequest = new RequestHarmoniQueryBills();

        ResponseHarmoniQueryBills result = mapper.toResponseHarmoniQueryBills(queryBillsResponse, harmoniRequest);

        assertNotNull(result);
    }

    @Test
    public void testToDoBillPaymentRequest() {
        RequestHarmoniDoBillPayment request = new RequestHarmoniDoBillPayment();
        request.setPaymentSourceCode("CARD");
        BalanceAccountPaymentInstrumentDTO balanceAccount = new BalanceAccountPaymentInstrumentDTO();
        balanceAccount.setAccountCurrencyCode("USD");
        request.setBalanceAccountPaymentInstrument(balanceAccount);

        DoBillPaymentRequest result = mapper.toDoBillPaymentRequest(request);

        assertNotNull(result);
        assertEquals("USD", result.getCurrency());
    }

    @Test
    public void testToResponseHarmoniDoBillPaymentResultDTO() {
        DoBillPaymentResponse microResponse = new DoBillPaymentResponse();
        microResponse.setContractNumber("123456");

        ResponseHarmoniDoBillPaymentResultDTO result = mapper.toResponseHarmoniDoBillPaymentResultDTO(microResponse);

        assertEquals("123456", result.getContractNo());
    }

    @Test
    public void testToCancelBillPaymentRequest() {
        RequestHarmoniCancelBillPayment request = new RequestHarmoniCancelBillPayment();
        request.setContractNo("123456");

        CancelBillPaymentRequest result = mapper.toCancelBillPaymentRequest(request);

        assertEquals("123456", result.getContractNumber());
    }

    @Test
    public void testToResponseHarmoniCancelBillPayment() {
        CancelBillPaymentResponse microResponse = new CancelBillPaymentResponse();

        ResponseHarmoniCancelBillPayment result = mapper.toResponseHarmoniCancelBillPayment(microResponse);

        assertNotNull(result);
    }

    @Test
    public void testToGetBillPaymentExpenseRequestDTO() {
        RequestHarmoniGetBillPaymentExpense request = new RequestHarmoniGetBillPaymentExpense();
        request.setCreditCardNo("1234");

        GetBillPaymentExpenseRequestDTO result = mapper.toGetBillPaymentExpenseRequestDTO(request);

        assertEquals("1234", result.getCardNo());
    }

    @Test
    public void testToResponseHarmoniGetBillPaymentExpense() {
        GetBillPaymentExpenseResponseDTO microResponse = new GetBillPaymentExpenseResponseDTO();
        microResponse.setCommissionAmount(100);

        ResponseHarmoniGetBillPaymentExpense result = mapper.toResponseHarmoniGetBillPaymentExpense(microResponse);

        assertEquals(100, result.getExpenseAmount());
    }

    @Test
    public void testExtractCurrency() {
        RequestHarmoniDoBillPayment request = new RequestHarmoniDoBillPayment();
        BalanceAccountPaymentInstrumentDTO balanceAccount = new BalanceAccountPaymentInstrumentDTO();
        balanceAccount.setAccountCurrencyCode("USD");
        request.setBalanceAccountPaymentInstrument(balanceAccount);
        request.setPaymentSourceCode("ACCOUNT");

        String result = mapper.extractCurrency(request);

        assertEquals("USD", result);
    }

    @Test
    public void testToCartPaymentMethodDetail() {
        RequestHarmoniDoBillPayment request = new RequestHarmoniDoBillPayment();
        CreditCardPaymentInstumentDTO creditCard = new CreditCardPaymentInstumentDTO();
        creditCard.setCardNumber("1234");
        request.setCreditCardPaymentInstrument(creditCard);
        request.setPaymentSourceCode("CARD");

        CreditCardPaymentMethodDetailWebDTO result = mapper.toCartPaymentMethodDetail(request);

        assertNotNull(result);
        assertEquals("1234", result.getCardNumber());
    }

    @Test
    public void testToCashPaymentMethodDetail() {
        RequestHarmoniDoBillPayment request = new RequestHarmoniDoBillPayment();
        request.setPaymentSourceCode("CASH");

        CashPaymentMethodDetailWebDTO result = mapper.toCashPaymentMethodDetail(request);

        assertNull(result);
    }

    @Test
    public void testToAccountPaymentMethodDetail() {
        RequestHarmoniDoBillPayment request = new RequestHarmoniDoBillPayment();
        BalanceAccountPaymentInstrumentDTO balanceAccount = new BalanceAccountPaymentInstrumentDTO();
        balanceAccount.setAccountNumber("1234");
        request.setBalanceAccountPaymentInstrument(balanceAccount);
        request.setPaymentSourceCode("ACCOUNT");

        AccountPaymentMethodDetailWebDTO result = mapper.toMicroPaymentSource(request);

        assertNotNull(result);
        assertEquals("1234", result.getAccountNo());
    }

    @Test
    public void testToMicroPaymentSource() {
        String hmnPaymentSource = "CARD";
        BillTransactionConstant.hmnMicroPaymentMap.put(hmnPaymentSource, "MICRO_CARD");

        String result = mapper.toMicroPaymentSource(hmnPaymentSource);

        assertEquals("MICRO_CARD", result);
    }

    @Test
    public void testToHarmoniSubscriberNo1() {
        List<SubsrciberNoPartResponseWebDTO> subscriberNoPartList = List.of(new SubsrciberNoPartResponseWebDTO(1, "1234"));

        String result = mapper.toHarmoniSubscriberNo1(subscriberNoPartList);

        assertEquals("1234", result);
    }

    @Test
    public void testToHarmoniSubscriberNo2() {
        List<SubsrciberNoPartResponseWebDTO> subscriberNoPartList = List.of(
                new SubsrciberNoPartResponseWebDTO(1, "1234"),
                new SubsrciberNoPartResponseWebDTO(2, "5678")
        );

        String result = mapper.toHarmoniSubscriberNo2(subscriberNoPartList);

        assertEquals("5678", result);
    }

    @Test
    public void testToHarmoniSubscriberNo3() {
        List<SubsrciberNoPartResponseWebDTO> subscriberNoPartList = List.of(
                new SubsrciberNoPartResponseWebDTO(1, "1234"),
                new SubsrciberNoPartResponseWebDTO(2, "5678"),
                new SubsrciberNoPartResponseWebDTO(3, "91011")
        );

        String result = mapper.toHarmoniSubscriberNo3(subscriberNoPartList);

        assertEquals("91011", result);
    }

    @Test
    public void testHarmoniToMicroChannel() {
        String harmoniChannel = "WEB";
        String expectedMicroChannel = "WEB_MICRO"; // Assuming the ChannelUtil.convertChannel method maps "WEB" to "WEB_MICRO"
        when(ChannelUtil.convertChannel(harmoniChannel)).thenReturn(expectedMicroChannel);

        String result = mapper.harmoniToMicroChannel(harmoniChannel);

        assertEquals(expectedMicroChannel, result);
    }

    @Test
    public void testToSubscriberNoPartList() {
        RequestHarmoniQueryBills request = new RequestHarmoniQueryBills();
        request.setSubscriberNo1("1234");
        request.setSubscriberNo2("5678");
        request.setSubscriberNo3("91011");

        List<SubscriberNoPartRequestWebDTO> result = mapper.toSubscriberNoPartList(request);

        assertEquals(3, result.size());
        assertEquals("1234", result.get(0).getPartValue());
        assertEquals("5678", result.get(1).getPartValue());
        assertEquals("91011", result.get(2).getPartValue());
    }

    @Test
    public void testAfterToGetQueryBillsResponse() {
        // Implement the test for the @AfterMapping method if necessary.
    }

    @Test
    public void testExtractOperatingBranchCode() {
        String operatingBranchCode = "branch";

        String result = mapper.extractOperatingBranchCode(operatingBranchCode);

        assertEquals("branch", result);

        String emptyBranchCode = "";

        result = mapper.extractOperatingBranchCode(emptyBranchCode);

        assertEquals(BillTransactionConstant.GENERAL_BRANCH_CODE, result);
    }
}
