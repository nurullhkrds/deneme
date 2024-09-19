import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

class AccountingConstantTest {

    @Test
    void testAccountingConstantValues() {
        // Test ana sabitler
        assertEquals("BILL", AccountingConstant.C_TYPE);
        assertEquals("PAY", AccountingConstant.D_TYPE);
        assertEquals("DKNTSABIT", AccountingConstant.DKNTSABIT);
    }

    @Test
    void testCreditCardProvisionConstants() {
        // Test CreditCardProvision sınıfı sabitleri
        assertEquals("OD19", AccountingConstant.CreditCardProvision.CARD_PROVISION_TERM_ID);
        assertEquals("PYMF", AccountingConstant.CreditCardProvision.CREDIT_CARD_TRANSACTION_TYPE);
        assertEquals("000000", AccountingConstant.CreditCardProvision.DO_PROVISION_SUCCESS_RESULT_CODE);
        assertEquals("0000000", AccountingConstant.CreditCardProvision.ACKNOWLEDGE_SUCCESS_IRC);
        assertEquals("B968705", AccountingConstant.CreditCardProvision.ACKNOWLEDGE_TRXN_HAS_ALREADY_BEEN_SETTLED_IRC);
    }

    @Test
    void testCreditCardProvisionConstructor() {
        // CreditCardProvision sınıfının kurucusunun çağrılamadığını test edin
        Exception exception = assertThrows(IllegalStateException.class, () -> {
            new AccountingConstant.CreditCardProvision();
        });
        assertEquals("CreditCardProvision: Constant class", exception.getMessage());
    }
}
