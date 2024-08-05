package com.ykb.payments.bill.transaction.process.query;

import static org.junit.jupiter.api.Assertions.*;

import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

import java.lang.reflect.Constructor;
import java.lang.reflect.InvocationTargetException;
import java.util.HashMap;
import java.util.List;
import java.util.ArrayList;
import java.util.Map;

import com.ykb.payments.bill.common.enums.EnumBillResult;
import com.ykb.payments.bill.common.exception.BillException;
import com.ykb.payments.bill.transaction.external.adapter.dto.QueriedBillDTO;
import com.ykb.payments.bill.transaction.external.adapter.response.QueryBillsAdapterResponse;
import com.ykb.payments.bill.transaction.external.harmoni.billpayment.rest.response.ResponseGetCustomerPaidBillList;
import com.ykb.payments.bill.transaction.external.limitation.model.PaymentAllowedResponse;
import com.ykb.payments.bill.transaction.institution.mapper.InstitutionUserIntfMapper;
import com.ykb.payments.bill.transaction.process.common.AbstractProcess;

import com.ykb.payments.bill.transaction.external.adapter.service.AdapterService;
import com.ykb.payments.bill.transaction.external.harmoni.billpayment.rest.service.BillPaymentRestFacade;
import com.ykb.payments.bill.transaction.external.limitation.service.LimitationService;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionUserIntfDTO;
import com.ykb.payments.bill.transaction.institution.service.InstitutionUserIntService;
import com.ykb.payments.bill.transaction.institution.util.SubscriberNumberUtils;
import com.ykb.payments.bill.transaction.payment.dto.SubscriberNoPartRequestDTO;
import com.ykb.payments.bill.transaction.payment.event.PaymentEventPublisher;
import com.ykb.payments.bill.transaction.payment.mapper.PaymentMapper;
import com.ykb.payments.bill.transaction.payment.repository.PaymentRepository;
import com.ykb.payments.bill.transaction.payment.service.PaymentUtilImpl;
import com.ykb.payments.bill.transaction.payment.service.ProvisionService;
import com.ykb.payments.bill.transaction.process.common.ProcessDataPackKey;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;


import static org.mockito.ArgumentMatchers.any;
import static org.mockito.Mockito.when;


import java.util.Collections;

import org.mockito.Spy;

@ExtendWith(MockitoExtension.class)
public class QueryBillsProcessTest {

    @Mock
    private AdapterService adapterService;
    @Mock
    private ProvisionService provisionService;
    @Mock
    private InstitutionUserIntService institutionUserIntService;
    @Mock
    private InstitutionUserIntfMapper institutionUserIntMapper;
    @Mock
    private BillPaymentRestFacade billPaymentRestFacade;
    @Mock
    private PaymentRepository paymentRepository;
    @Mock
    private PaymentMapper paymentMapper;
    @Mock
    private LimitationService limitationService;
    @Mock
    private PaymentEventPublisher paymentEventPublisher;
    @Mock
    private PaymentUtilImpl paymentUtilImpl;

    @Spy
    @InjectMocks
    private QueryBillsProcess queryBillsProcess;

    @BeforeEach
    void setUp() {
        // Mock the dataPack method from AbstractProcess
        when(queryBillsProcess.getDataPack().get(ProcessDataPackKey.CUSTOMER_NO.getKey())).thenReturn(123L);
        when(queryBillsProcess.getDataPack().get(ProcessDataPackKey.IDENTITY_NO.getKey())).thenReturn(456L);
        when(queryBillsProcess.getDataPack().get((String) ProcessDataPackKey.TAX_ID.getKey())).thenReturn("12345");
        when(queryBillsProcess.getDataPack().get((String) ProcessDataPackKey.SUBSCRIBER_NO.getKey())).thenReturn("67890");
        when(queryBillsProcess.getDataPack().get(ProcessDataPackKey.SUBSCRIBER_NO_PART_LIST.getKey())).thenReturn(Collections.emptyList());
        when(queryBillsProcess.getDataPack().get((String) ProcessDataPackKey.CURRENCY.getKey())).thenReturn("USD");

        when(paymentUtilImpl.isFomOperationEnabled(any())).thenReturn(true);
    }

    @Test
    void testExecuteProcessSuccess() throws BillException {
        // Mock responses for various services
        when(adapterService.queryBills(any(), any(), any())).thenReturn(mockQueryBillsAdapterResponse(EnumBillResult.SUCCESS));
        when(billPaymentRestFacade.getCustomerPaidBillList(any(), any(), any())).thenReturn(new ResponseGetCustomerPaidBillList());
        when(paymentRepository.findPaidBillList(any(), any(), any())).thenReturn(Collections.emptyList());
        when(provisionService.createProvisions(any())).thenReturn(Collections.emptyList());

        queryBillsProcess.executeProcess();

        // Add assertions to verify the results
    }

    @Test
    void testExecuteProcessSubscriberNumberInvalid() {
        // Mock responses to invalidate the subscriber number
        when(SubscriberNumberUtils.checkSubscriberNumberParts(any(), any())).thenReturn(false);

        try {
            queryBillsProcess.executeProcess();
        } catch (BillException e) {
            assert(e.getMessage().equals(EnumBillResult.SUBSCRIBER_NUMBER_INVALID.name()));
        }
    }

    @Test
    void testExecuteProcessBillQueryLimitReached() throws BillException {
        // Mock responses to reach the query limit
        when(limitationService.isPaymentAllowedWithoutDebtOwner(any(), any(), any(), any())).thenReturn(mockPaymentAllowedResponse(false));

        try {
            queryBillsProcess.executeProcess();
        } catch (BillException e) {
            assert(e.getMessage().equals(EnumBillResult.BILL_QUERY_LIMIT_REACHED.name()));
        }
    }

    @Test
    void testExecuteProcessBillNotFound() throws BillException {
        // Mock responses to simulate no bills found
        when(adapterService.queryBills(any(), any(), any())).thenReturn(mockQueryBillsAdapterResponse(EnumBillResult.SUCCESS));
        when(billPaymentRestFacade.getCustomerPaidBillList(any(), any(), any())).thenReturn(new ResponseGetCustomerPaidBillList());
        when(paymentRepository.findPaidBillList(any(), any(), any())).thenReturn(Collections.emptyList());

        try {
            queryBillsProcess.executeProcess();
        } catch (BillException e) {
            assert(e.getMessage().equals(EnumBillResult.BILL_NOT_FOUND.name()));
        }
    }

    private QueryBillsAdapterResponse mockQueryBillsAdapterResponse(EnumBillResult result) {
        QueryBillsAdapterResponse response = new QueryBillsAdapterResponse();
        response.setInternalResultCode(result.getHmnCode().toString());
        if (result == EnumBillResult.SUCCESS) {
            response.setBills(Collections.singletonList(new QueriedBillDTO()));
        }
        return response;
    }

    private PaymentAllowedResponse mockPaymentAllowedResponse(boolean isAllowed) {
        PaymentAllowedResponse response = new PaymentAllowedResponse();
        response.setPaymentAllowed(isAllowed);
        return response;
    }
}

java.lang.NullPointerException: Cannot invoke "java.util.Map.get(Object)" because the return value of "com.ykb.payments.bill.transaction.process.query.QueryBillsProcess.getDataPack()" is null
