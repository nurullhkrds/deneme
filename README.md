import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.*;

public class AccountingConstantTest {

    @Test
    public void testAccountingConstantValues() {
        // AccountingConstant sabitlerinin doğru değerlerle tanımlandığını kontrol et
        assertEquals("BILL", AccountingConstant.C_TYPE);
        assertEquals("PAY", AccountingConstant.D_TYPE);
        assertEquals("DKNTSABIT", AccountingConstant.DKNTSABIT);
    }

    @Test
    public void testCreditCardProvisionConstants() {
        // CreditCardProvision sabitlerinin doğru değerlerle tanımlandığını kontrol et
        assertEquals("OD19", AccountingConstant.CreditCardProvision.CARD_PROVISION_TERM_ID);
        assertEquals("PYMF", AccountingConstant.CreditCardProvision.CREDIT_CARD_TRANSACTION_TYPE);
        assertEquals("000000", AccountingConstant.CreditCardProvision.DO_PROVISION_SUCCESS_RESULT_CODE);
        assertEquals("0000000", AccountingConstant.CreditCardProvision.ACKNOWLEDGE_SUCCESS_IRC);
        assertEquals("B968705", AccountingConstant.CreditCardProvision.ACKNOWLEDGE_TRXN_HAS_ALREADY_BEEN_SETTLED_IRC);
    }

    @Test
    public void testCreditCardProvisionConstructor() {
        // CreditCardProvision sınıfının örneği oluşturulamayacağını kontrol et
        assertThrows(IllegalStateException.class, () -> {
            new AccountingConstant.CreditCardProvision();
        });
    }
}
