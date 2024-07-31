import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertNotNull;

import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;
import java.util.Date;
import java.util.List;

import org.junit.jupiter.api.Test;
import org.mapstruct.factory.Mappers;

public class PaymentMapperTest {

    private PaymentMapper mapper = Mappers.getMapper(PaymentMapper.class);

    @Test
    public void testToEntity() {
        PaymentDTO dto = new PaymentDTO();
        dto.setPaymentTime("12:34:56");
        
        Payment entity = mapper.toEntity(dto);

        assertNotNull(entity);
        assertEquals("12:34:56", entity.getPaymentTime());
    }

    @Test
    public void testToDTO() {
        Payment entity = new Payment();
        entity.setPaymentTime("12:34:56");

        PaymentDTO dto = mapper.toDTO(entity);

        assertNotNull(dto);
        assertEquals("12:34:56", dto.getPaymentTime());
    }

    @Test
    public void testToPaidBillResponseWebDTOFromPaymentAndInstitution() {
        PaymentDTO payment = new PaymentDTO();
        payment.setId(1L);
        payment.setExplanation("Test Explanation");
        payment.setAccountNo("12345");
        payment.setAmount(100.0);
        payment.setPaymentAmount(90.0);
        payment.setCreditCardNo("67890");
        payment.setContractNo("112233");
        payment.setInstitutionDebtTypeId(1);
        payment.setCurrency(new CurrencyDTO("USD"));

        InstitutionDTO institution = new InstitutionDTO();
        ProductDTO product = new ProductDTO();
        product.setCode("PROD123");
        institution.setProduct(product);

        PaidBillResponseWebDTO dto = mapper.toPaidBillResponseWebDTO(payment, institution);

        assertNotNull(dto);
        assertEquals(1L, dto.getId());
        assertEquals("Test Explanation", dto.getExplanation());
        assertEquals("12345", dto.getAccountNumber());
        assertEquals(100.0, dto.getBillAmount());
        assertEquals(90.0, dto.getBillRecalculatedAmount());
        assertEquals("67890", dto.getCardNumber());
        assertEquals("112233", dto.getContractNumber());
        assertEquals(1, dto.getDebtTypeId());
        assertEquals("PROD123", dto.getProductCode());
        assertEquals("USD", dto.getCurrency());
    }

    @Test
    public void testToPaidBillResponseWebDTOFromHmnPaidBill() {
        HmnPaidBillDTO hmnPaidBill = new HmnPaidBillDTO();
        PaymentInformationDTO paymentInformation = new PaymentInformationDTO();
        BalanceAccountPaymentInstrumentDTO balanceAccount = new BalanceAccountPaymentInstrumentDTO();
        balanceAccount.setAccountNumber("12345");
        paymentInformation.setBalanceAccountPaymentInstrument(balanceAccount);
        CreditCardPaymentInstumentDTO creditCard = new CreditCardPaymentInstumentDTO();
        creditCard.setCardNumber("67890");
        paymentInformation.setCreditCardPaymentInstrument(creditCard);
        paymentInformation.setAccountingContractNumber("112233");
        hmnPaidBill.setPaymentInformation(paymentInformation);
        hmnPaidBill.setProduct("PROD123");
        hmnPaidBill.setInstitution("INST123");
        hmnPaidBill.setBillDueDate(new Date());

        PaidBillResponseWebDTO dto = mapper.toPaidBillResponseWebDTO(hmnPaidBill);

        assertNotNull(dto);
        assertEquals("12345", dto.getAccountNumber());
        assertEquals("67890", dto.getCardNumber());
        assertEquals("112233", dto.getContractNumber());
        assertEquals("PROD123", dto.getProductCode());
        assertEquals("INST123", dto.getInstitutionCode());
        assertNotNull(dto.getBillDueDate());
    }

    @Test
    public void testToPaidBillAdapterDTO() {
        Payment payment = new Payment();
        payment.setCurrency(new CurrencyDTO("USD"));

        PaidBillAdapterDTO dto = mapper.toPaidBillAdapterDTO(payment);

        assertNotNull(dto);
        assertEquals("USD", dto.getCurrency());
    }

    @Test
    public void testToCancelledBillAdapterDTO() {
        Payment payment = new Payment();
        payment.setCurrency(new CurrencyDTO("USD"));

        PaymentCancelDTO paymentCancel = new PaymentCancelDTO();
        paymentCancel.setChannelSessionId("SESSION123");
        paymentCancel.setChannelTransactionId("TRANS123");
        paymentCancel.setId(1L);
        paymentCancel.setBranchCode("BRANCH123");
        paymentCancel.setChannelCode("CHANNEL123");
        paymentCancel.setCreateDate(LocalDateTime.now());

        CancelledBillAdapterDTO dto = mapper.toCancelledBillAdapterDTO(payment, paymentCancel);

        assertNotNull(dto);
        assertEquals("SESSION123", dto.getChannelSessionId());
        assertEquals("TRANS123", dto.getChannelTransactionId());
        assertEquals(1L, dto.getId());
        assertEquals("BRANCH123", dto.getBranchCode());
        assertEquals("CHANNEL123", dto.getChannelCode());
        assertNotNull(dto.getCancelTime());
        assertEquals("USD", dto.getCurrency());
    }

    @Test
    public void testToNotifyPaymentResponse() {
        NotifyPaymentProcessOutput result = new NotifyPaymentProcessOutput();
        result.setCode("SUCCESS");
        result.setExplanation("Transaction Successful");

        NotifyPaymentResponse response = mapper.toNotifyPaymentResponse(result);

        assertNotNull(response);
        assertEquals("SUCCESS", response.getReturnCode());
        assertEquals("Transaction Successful", response.getReturnMessage());
    }

    @Test
    public void testToNotifyPaymentCancelResponse() {
        NotifyPaymentCancelProcessOutput result = new NotifyPaymentCancelProcessOutput();
        result.setCode("SUCCESS");
        result.setExplanation("Cancellation Successful");

        NotifyPaymentCancelResponse response = mapper.toNotifyPaymentCancelResponse(result);

        assertNotNull(response);
        assertEquals("SUCCESS", response.getReturnCode());
        assertEquals("Cancellation Successful", response.getReturnMessage());
    }

    @Test
    public void testToHmnPaidBillDTO() {
        PaidBillResponseWebDTO paidBillResponseWebDTO = new PaidBillResponseWebDTO();
        paidBillResponseWebDTO.setBillDueDate("2024-07-31");
        paidBillResponseWebDTO.setProductCode("PROD123");
        paidBillResponseWebDTO.setInstitutionCode("INST123");
        paidBillResponseWebDTO.setContractNumber("112233");

        HmnPaidBillDTO dto = mapper.toHmnPaidBillDTO(paidBillResponseWebDTO);

        assertNotNull(dto);
        assertNotNull(dto.getBillDueDate());
        assertEquals("PROD123", dto.getProduct());
        assertEquals("INST123", dto.getInstitution());
        assertEquals("112233", dto.getBankReferenceNo());
    }

    @Test
    public void testToHmnRecoDetailDTO() {
        Payment payment = new Payment();
        payment.setCurrency(new CurrencyDTO("USD"));
        payment.setStatus(new StatusDTO("COMPLETED"));
        payment.setAccountNo("12345");
        payment.setCreditCardNo("67890");
        payment.setContractNo("112233");
        payment.setPaymentDate(LocalDateTime.now());

        HmnPaidBillDTO dto = mapper.toHmnRecoDetailDTO(payment);

        assertNotNull(dto);
        assertEquals("USD", dto.getCurrency());
        assertEquals("COMPLETED", dto.getStatus());
        assertEquals("12345", dto.getPaymentInformation().getBalanceAccountPaymentInstrument().getAccountNumber());
        assertEquals("67890", dto.getPaymentInformation().getCreditCardPaymentInstrument().getCardNumber());
        assertEquals("112233", dto.getPaymentInformation().getAccountingContractNumber());
        assertNotNull(dto.getPaymentInformation().getPaymentDate());
    }

    @Test
    public void testToHmnRecoDetailDTOList() {
        Payment payment = new Payment();
        payment.setCurrency(new CurrencyDTO("USD"));
        payment.setStatus(new StatusDTO("COMPLETED"));
        payment.setAccountNo("12345");
        payment.setCreditCardNo("67890");
        payment.setContractNo("112233");
        payment.setPaymentDate(LocalDateTime.now());

        List<Payment> payments = List.of(payment);
        List<HmnPaidBillDTO> dtoList = mapper.toHmnRecoDetailDTOList(payments);

        assertNotNull(dtoList);
        assertEquals(1, dtoList.size());

        HmnPaidBillDTO dto = dtoList.get(0);
        assertEquals("USD", dto.getCurrency());
        assertEquals("COMPLETED", dto.getStatus());
        assertEquals("12345", dto.getPaymentInformation().getBalanceAccountPaymentInstrument().getAccountNumber());
        assertEquals("67890", dto.getPaymentInformation().getCreditCardPaymentInstrument().getCardNumber());
        assertEquals("112233", dto.getPaymentInformation().getAccountingContractNumber());
        assertNotNull(dto.getPaymentInformation().getPaymentDate());
    }
}
