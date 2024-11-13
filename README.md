package com.ykb.payments.bill.transaction.institution.admin.service.impl;

import com.ykb.architecture.micro.error.exception.DataNotFoundException;
import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.common.exception.BillExceptionsUI;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminPaymentMethodService;
import com.ykb.payments.bill.transaction.institution.domain.PaymentMethod;
import com.ykb.payments.bill.transaction.institution.dto.PaymentMethodDTO;
import com.ykb.payments.bill.transaction.institution.enums.EnumPaymentMethod;
import com.ykb.payments.bill.transaction.institution.mapper.PaymentMethodMapper;
import com.ykb.payments.bill.transaction.institution.repository.PaymentMethodRepository;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

public class AdminPaymentMethodServiceTest {

    @InjectMocks
    private AdminPaymentMethodServiceImpl adminPaymentMethodService;

    @Mock
    private PaymentMethodRepository paymentMethodRepository;

    @Mock
    private PaymentMethodMapper paymentMethodMapper;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void getPaymentMethodByMethod_WhenFound_ShouldReturnDTO() throws MicroException {
        EnumPaymentMethod paymentMethod = EnumPaymentMethod.CARD;
        PaymentMethod paymentMethodEntity = new PaymentMethod();
        PaymentMethodDTO paymentMethodDTO = new PaymentMethodDTO();

        when(paymentMethodRepository.findByCode(paymentMethod)).thenReturn(paymentMethodEntity);
        when(paymentMethodMapper.toDTO(paymentMethodEntity)).thenReturn(paymentMethodDTO);

        PaymentMethodDTO result = adminPaymentMethodService.getPaymentMethodByMethod(paymentMethod);

        assertNotNull(result);
        verify(paymentMethodRepository, times(1)).findByCode(paymentMethod);
        verify(paymentMethodMapper, times(1)).toDTO(paymentMethodEntity);
    }

    @Test
    void getPaymentMethodByMethod_WhenNotFound_ShouldThrowException() {
        EnumPaymentMethod paymentMethod = EnumPaymentMethod.CARD;

        when(paymentMethodRepository.findByCode(paymentMethod)).thenReturn(null);

        assertThrows(DataNotFoundException.class, () -> adminPaymentMethodService.getPaymentMethodByMethod(paymentMethod));
        verify(paymentMethodRepository, times(1)).findByCode(paymentMethod);
    }
}
