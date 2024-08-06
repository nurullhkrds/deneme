package com.ykb.payments.bill.transaction.common.exception;

import com.ykb.architecture.micro.error.exception.data.ExceptionData;
import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.*;

public class PaymentExceptionsTest {

    @Test
    void testPaymentExceptionsConstants() {
        assertNotNull(PaymentExceptions.SUCCESS);
        assertEquals("aaaa-transaction", PaymentExceptions.SHORT_APP_NAME);
    }

    @Test
    void testValidationExceptionsConstants() {
        assertNotNull(PaymentExceptions.ValidationExceptions.SERVICE_DEFINITION_NOT_FOUND);
        assertNotNull(PaymentExceptions.ValidationExceptions.TOKEN_RECORD_NOT_FOUND);
        // Add similar assertions for other constants
    }

    @Test
    void testAccountingExceptionsConstants() {
        assertNotNull(PaymentExceptions.AccountingExceptions.COMMISSION_NOT_FOUND);
        assertNotNull(PaymentExceptions.AccountingExceptions.COMMISSION_GENERIC_UNKNOWN_ERROR);
        // Add similar assertions for other constants
    }

    @Test
    void testPaymentExceptionsConstructorThrowsException() {
        Exception exception = assertThrows(IllegalStateException.class, PaymentExceptions::new);
        assertEquals("PaymentExceptions: Constant class", exception.getMessage());
    }

    @Test
    void testValidationExceptionsConstructorThrowsException() {
        Exception exception = assertThrows(IllegalStateException.class, PaymentExceptions.ValidationExceptions::new);
        assertEquals("ValidationExceptions: Constant class", exception.getMessage());
    }

    @Test
    void testAccountingExceptionsConstructorThrowsException() {
        Exception exception = assertThrows(IllegalStateException.class, PaymentExceptions.AccountingExceptions::new);
        assertEquals("AccountingExceptions: Constant class", exception.getMessage());
    }
}
