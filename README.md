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
import org.springframework.stereotype.Service;

@Service
public class AdminPaymentMethodServiceImpl implements AdminPaymentMethodService {

    private final PaymentMethodRepository paymentMethodRepository;
    private final PaymentMethodMapper paymentMethodMapper;

    public AdminPaymentMethodServiceImpl(PaymentMethodRepository paymentMethodRepository, PaymentMethodMapper paymentMethodMapper) {
        this.paymentMethodRepository = paymentMethodRepository;
        this.paymentMethodMapper = paymentMethodMapper;
    }



    @Override
    public PaymentMethodDTO getPaymentMethodByMethod(EnumPaymentMethod paymentMethod) throws MicroException {
        PaymentMethod entity= paymentMethodRepository.findByCode(paymentMethod);
        if (entity == null ){
            throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.PAYMENT_METHOD_NOT_FOUND);
        }
        PaymentMethodDTO dto= paymentMethodMapper.toDTO(entity);
        return dto;



    }
}
