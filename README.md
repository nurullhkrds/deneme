package com.ykb.payments.bill.transaction.payment.consumer;

import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.*;

import com.ykb.payments.bill.common.enums.EnumBillResult;
import com.ykb.payments.bill.transaction.payment.model.CreditCardProvisionACKEventDTO;
import com.ykb.payments.bill.transaction.payment.model.CreditCardProvisionReverseEventDTO;
import com.ykb.payments.bill.transaction.payment.model.PaymentCancelNotificationEvent;
import com.ykb.payments.bill.transaction.payment.model.PaymentNotificationEvent;
import com.ykb.payments.bill.transaction.payment.service.PaymentNotificationService;
import com.ykb.payments.bill.transaction.payment.service.PaymentService;
import com.ykb.payments.bill.transaction.process.notifypayment.NotifyPaymentProcessOutput;
import com.ykb.payments.bill.transaction.process.notifypaymentcancel.NotifyPaymentCancelProcessOutput;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.test.util.ReflectionTestUtils;

import org.springframework.test.context.junit.jupiter.SpringExtension;

import java.util.logging.Logger;

import static org.junit.jupiter.api.Assertions.*;

@ExtendWith(MockitoExtension.class)
@ExtendWith(SpringExtension.class)
public class BillEventConsumerTest {

    @Mock
    private PaymentService paymentService;

    @Mock
    private PaymentNotificationService paymentNotificationService;

    @InjectMocks
    private BillEventConsumer billEventConsumer;

    @BeforeEach
    void setUp() {
        ReflectionTestUtils.setField(billEventConsumer, "logger", mock(Logger.class));
    }

    @Test
    public void testOnMessage_PaymentNotificationEvent_Success() throws Exception {
        PaymentNotificationEvent event = new PaymentNotificationEvent();
        NotifyPaymentProcessOutput output = new NotifyPaymentProcessOutput();
        output.setResult(EnumBillResult.SUCCESS);

        when(paymentService.notifyPayment(any(PaymentNotificationEvent.class))).thenReturn(output);

        billEventConsumer.onMessage(event);

        verify(paymentService, times(1)).notifyPayment(any(PaymentNotificationEvent.class));
    }

    @Test
    public void testOnMessage_PaymentNotificationEvent_Failure() throws Exception {
        PaymentNotificationEvent event = new PaymentNotificationEvent();
        NotifyPaymentProcessOutput output = new NotifyPaymentProcessOutput();
        output.setResult(EnumBillResult.BILL_NOT_FOUND);

        when(paymentService.notifyPayment(any(PaymentNotificationEvent.class))).thenReturn(output);

        Exception exception = assertThrows(RuntimeException.class, () -> {
            billEventConsumer.onMessage(event);
        });

        assertEquals("Notification is unsuccessfull", exception.getMessage());
        verify(paymentService, times(1)).notifyPayment(any(PaymentNotificationEvent.class));
    }

    @Test
    public void testOnMessage_PaymentCancelNotificationEvent_Success() throws Exception {
        PaymentCancelNotificationEvent event = new PaymentCancelNotificationEvent();
        NotifyPaymentCancelProcessOutput output = new NotifyPaymentCancelProcessOutput();
        output.setResult(EnumBillResult.SUCCESS);

        when(paymentService.notifyPaymentCancel(any(PaymentCancelNotificationEvent.class))).thenReturn(output);

        billEventConsumer.onMessage(event);

        verify(paymentService, times(1)).notifyPaymentCancel(any(PaymentCancelNotificationEvent.class));
    }

    @Test
    public void testOnMessage_PaymentCancelNotificationEvent_Failure() throws Exception {
        PaymentCancelNotificationEvent event = new PaymentCancelNotificationEvent();
        NotifyPaymentCancelProcessOutput output = new NotifyPaymentCancelProcessOutput();
        output.setResult(EnumBillResult.BILL_NOT_FOUND);

        when(paymentService.notifyPaymentCancel(any(PaymentCancelNotificationEvent.class))).thenReturn(output);

        Exception exception = assertThrows(RuntimeException.class, () -> {
            billEventConsumer.onMessage(event);
        });

        assertEquals("Notification is unsuccessfull", exception.getMessage());
        verify(paymentService, times(1)).notifyPaymentCancel(any(PaymentCancelNotificationEvent.class));
    }

    @Test
    public void testProcessCreditCardProvisionACKMessage_Success() throws Exception {
        CreditCardProvisionACKEventDTO event = new CreditCardProvisionACKEventDTO();
        event.setPaymentId(12345L);
        event.setPaymentNotificationId(12345L);

        doNothing().when(paymentNotificationService).sendAckForCreditCardProvision(anyLong(), anyLong(), anyBoolean());

        billEventConsumer.processCreditCardProvisionACKMessage(event);

        verify(paymentNotificationService, times(1)).sendAckForCreditCardProvision(anyLong(), anyLong(), anyBoolean());
    }

    @Test
    public void testProcessCreditCardProvisionReverseMessage_Success() throws Exception {
        CreditCardProvisionReverseEventDTO event = new CreditCardProvisionReverseEventDTO();
        event.setPaymentId(12345L);
        event.setPaymentNotificationId(12345L);
        event.setPaymentCancelId(12345L);

        doNothing().when(paymentNotificationService).creditCardReverseProvision(anyLong(), anyLong(), anyLong(), anyBoolean());

        billEventConsumer.processCreditCardProvisionReverseMessage(event);

        verify(paymentNotificationService, times(1)).creditCardReverseProvision(anyLong(), anyLong(), anyLong(), anyBoolean());
    }

    @Test
    public void testProcessCreditCardProvisionACKMessage_Exception() throws Exception {
        CreditCardProvisionACKEventDTO event = new CreditCardProvisionACKEventDTO();
        event.setPaymentId(12345L);
        event.setPaymentNotificationId(12345L);

        doThrow(new RuntimeException("Test Exception")).when(paymentNotificationService).sendAckForCreditCardProvision(anyLong(), anyLong(), anyBoolean());

        Exception exception = assertThrows(RuntimeException.class, () -> {
            billEventConsumer.processCreditCardProvisionACKMessage(event);
        });

        assertEquals("Test Exception", exception.getMessage());
        verify(paymentNotificationService, times(1)).sendAckForCreditCardProvision(anyLong(), anyLong(), anyBoolean());
    }

    @Test
    public void testProcessCreditCardProvisionReverseMessage_Exception() throws Exception {
        CreditCardProvisionReverseEventDTO event = new CreditCardProvisionReverseEventDTO();
        event.setPaymentId(12345L);
        event.setPaymentNotificationId(12345L);
        event.setPaymentCancelId(12345L);

        doThrow(new RuntimeException("Test Exception")).when(paymentNotificationService).creditCardReverseProvision(anyLong(), anyLong(), anyLong(), anyBoolean());

        Exception exception = assertThrows(RuntimeException.class, () -> {
            billEventConsumer.processCreditCardProvisionReverseMessage(event);
        });

        assertEquals("Test Exception", exception.getMessage());
        verify(paymentNotificationService, times(1)).creditCardReverseProvision(anyLong(), anyLong(), anyLong(), anyBoolean());
    }
}



java.lang.IllegalStateException: Could not access method or field: Can not set static final org.slf4j.Logger field com.ykb.payments.bill.transaction.payment.consumer.BillEventConsumer.logger to org.mockito.codegen.Logger$MockitoMock$WCHNAuZy

