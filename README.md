import static org.junit.jupiter.api.Assertions.assertEquals;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

import java.math.BigDecimal;
import java.time.LocalDate;

public class EnumAccountingNarrativeTest {

    private CreateAccountingDTO createAccountingDTO;
    private ProvisionDTO provisionDTO;
    private Institution institution;

    @BeforeEach
    void setup() {
        createAccountingDTO = new CreateAccountingDTO();
        provisionDTO = new ProvisionDTO();
        institution = new Institution();

        Product product = new Product();
        product.setCode("P001");
        institution.setProduct(product);
        institution.setInstitutionCode("I001");
        createAccountingDTO.setInstitution(institution);

        provisionDTO.setSubscriberNo("SUB123");
        provisionDTO.setBillNo("BILL123");
        provisionDTO.setBillDueDate(LocalDate.now());
        provisionDTO.setBillTerm("TERM1");
        provisionDTO.setSubscriberName("John Doe");
        provisionDTO.setAdditionalInfo1("INFO1");
        provisionDTO.setAdditionalInfo2("INFO2");
        provisionDTO.setAdditionalInfo3("INFO3");
        provisionDTO.setAdditionalInfo4("INFO4");
        provisionDTO.setAdditionalInfo5("INFO5");
        provisionDTO.setAdditionalInfo6("INFO6");
        provisionDTO.setAdditionalInfo7("INFO7");
        provisionDTO.setAdditionalInfo8("INFO8");
        provisionDTO.setAdditionalInfo9("INFO9");
        createAccountingDTO.setProvisionDTO(provisionDTO);

        createAccountingDTO.setPaymentAmount(new BigDecimal("100.50"));
        createAccountingDTO.setCurrency(EnumCurrencyCode.USD); // Enum sabiti doğrudan kullanıyoruz
    }

    @Test
    void testProductNarrative() {
        String result = EnumAccountingNarrative.PRODUCT.getNarrative("PRODUCT", createAccountingDTO); // Değiştirildi
        assertEquals("P001", result);
    }

    @Test
    void testInstitutionNarrative() {
        String result = EnumAccountingNarrative.INSTITUTION.getNarrative("INSTITUTION", createAccountingDTO); // Değiştirildi
        assertEquals("I001", result);
    }

    @Test
    void testSubscriberNoNarrative() {
        String result = EnumAccountingNarrative.SUBSCRIBER_NO.getNarrative("SUBSCRIBER_NO", createAccountingDTO); // Değiştirildi
        assertEquals("SUB123", result);
    }

    @Test
    void testBillNoNarrative() {
        String result = EnumAccountingNarrative.BILL_NO.getNarrative("BILL_NO", createAccountingDTO); // Değiştirildi
        assertEquals("BILL123", result);
    }

    @Test
    void testAmountNarrative() {
        String result = EnumAccountingNarrative.AMOUNT.getNarrative("AMOUNT", createAccountingDTO); // Değiştirildi
        assertEquals("100.50", result);
    }

    @Test
    void testCurrencyNarrative() {
        String result = EnumAccountingNarrative.CURRENCY.getNarrative("CURRENCY", createAccountingDTO); // Değiştirildi
        assertEquals("USD", result);
    }

    @Test
    void testNameNarrative() {
        String result = EnumAccountingNarrative.NAME.getNarrative("NAME", createAccountingDTO); // Değiştirildi
        assertEquals("John Doe", result);
    }

    @Test
    void testDueDateNarrative() {
        String result = EnumAccountingNarrative.DUE_DATE.getNarrative("DUE_DATE", createAccountingDTO); // Değiştirildi
        assertEquals(DateUtils.formatLocalDate(LocalDate.now(), DateUtils.DATE_FORMAT_DD_MM_YYYY_WITH_SLASH), result);
    }

    @Test
    void testTermNarrative() {
        String result = EnumAccountingNarrative.TERM.getNarrative("TERM", createAccountingDTO); // Değiştirildi
        assertEquals("TERM1", result);
    }

    @Test
    void testAdditionalInfo1Narrative() {
        String result = EnumAccountingNarrative.ADDITIONAL_INFO_1.getNarrative("ADDITIONAL_INFO_1", createAccountingDTO); // Değiştirildi
        assertEquals("INFO1", result);
    }

    @Test
    void testAdditionalInfo2Narrative() {
        String result = EnumAccountingNarrative.ADDITIONAL_INFO_2.getNarrative("ADDITIONAL_INFO_2", createAccountingDTO); // Değiştirildi
        assertEquals("INFO2", result);
    }

    @Test
    void testAdditionalInfo3Narrative() {
        String result = EnumAccountingNarrative.ADDITIONAL_INFO_3.getNarrative("ADDITIONAL_INFO_3", createAccountingDTO); // Değiştirildi
        assertEquals("INFO3", result);
    }

    @Test
    void testAdditionalInfo4Narrative() {
        String result = EnumAccountingNarrative.ADDITIONAL_INFO_4.getNarrative("ADDITIONAL_INFO_4", createAccountingDTO); // Değiştirildi
        assertEquals("INFO4", result);
    }

    @Test
    void testAdditionalInfo5Narrative() {
        String result = EnumAccountingNarrative.ADDITIONAL_INFO_5.getNarrative("ADDITIONAL_INFO_5", createAccountingDTO); // Değiştirildi
        assertEquals("INFO5", result);
    }

    @Test
    void testAdditionalInfo6Narrative() {
        String result = EnumAccountingNarrative.ADDITIONAL_INFO_6.getNarrative("ADDITIONAL_INFO_6", createAccountingDTO); // Değiştirildi
        assertEquals("INFO6", result);
    }

    @Test
    void testAdditionalInfo7Narrative() {
        String result = EnumAccountingNarrative.ADDITIONAL_INFO_7.getNarrative("ADDITIONAL_INFO_7", createAccountingDTO); // Değiştirildi
        assertEquals("INFO7", result);
    }

    @Test
    void testAdditionalInfo8Narrative() {
        String result = EnumAccountingNarrative.ADDITIONAL_INFO_8.getNarrative("ADDITIONAL_INFO_8", createAccountingDTO); // Değiştirildi
        assertEquals("INFO8", result);
    }

    @Test
    void testAdditionalInfo9Narrative() {
        String result = EnumAccountingNarrative.ADDITIONAL_INFO_9.getNarrative("ADDITIONAL_INFO_9", createAccountingDTO); // Değiştirildi
        assertEquals("INFO9", result);
    }

    @Test
    void testCreateNarrative_emptyText() {
        String result = EnumAccountingNarrative.createNarrative("", createAccountingDTO);
        assertEquals("SUB123-", result);
    }

    @Test
    void testCreateNarrative_withText() {
        String result = EnumAccountingNarrative.createNarrative("BILL_NO", createAccountingDTO); // Değiştirildi
        assertEquals("BILL123", result);
    }

}
