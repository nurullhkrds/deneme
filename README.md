import com.fasterxml.jackson.databind.ObjectMapper;
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class EnumAccountingSourceTest {

    @Test
    void testParse() {
        // Test for valid value
        EnumAccountingSource result = EnumAccountingSource.parse("PAYMENT");
        assertNotNull(result);
        assertEquals(EnumAccountingSource.PAYMENT, result);

        // Test for another valid value
        result = EnumAccountingSource.parse("MERCHANT");
        assertNotNull(result);
        assertEquals(EnumAccountingSource.MERCHANT, result);

        // Test for invalid value
        result = EnumAccountingSource.parse("INVALID");
        assertNull(result);
    }
com.fasterxml.jackson.databind.exc.InvalidDefinitionException: Problem with definition of [AnnotedClass com.ykb.payments.bill.transaction.payment.enums.EnumAccountingSource]: Multiple 'as-value' properties defined ([field com.ykb.payments.bill.transaction.payment.enums.EnumAccountingSource#value] vs [field com.ykb.payments.bill.transaction.payment.enums.EnumAccountingSource#description])
org.opentest4j.AssertionFailedError: 
Expected :EnumAccountingSource(value=PAYMENT, description=Muhasabe biz tarafından gerceklesmis)
Actual   :EnumAccountingSource.PAYMENT(value=PAYMENT, description=Muhasabe biz tarafından gerceklesmis)
    @Test
    void testSerialization() throws Exception {
        ObjectMapper objectMapper = new ObjectMapper();
        
        // Test for PAYMENT enum
        String paymentJson = objectMapper.writeValueAsString(EnumAccountingSource.PAYMENT);
        assertEquals("\"PAYMENT\"", paymentJson);

        // Test for MERCHANT enum
        String merchantJson = objectMapper.writeValueAsString(EnumAccountingSource.MERCHANT);
        assertEquals("\"MERCHANT\"", merchantJson);
    }

    @Test
    void testToString() {
        assertEquals("EnumAccountingSource(value=PAYMENT, description=Muhasabe biz tarafından gerceklesmis)", EnumAccountingSource.PAYMENT.toString());
        assertEquals("EnumAccountingSource(value=MERCHANT, description=Muhasebe üye iş yeri ekibi tarafından gerçekleşmiş)", EnumAccountingSource.MERCHANT.toString());
    }
}
