@Test
void testExecuteProcessSuccess() throws BillException {
    // Mock SpringUtil behavior
    when(SpringUtil.getBean(ProcessService.class)).thenReturn(processService);
    when(SpringUtil.getBean(InstitutionFeatureService.class)).thenReturn(institutionFeatureService);
    when(SpringUtil.getBean(PaymentNotificationService.class)).thenReturn(paymentNotificationService);
    when(SpringUtil.getBean(PaymentService.class)).thenReturn(paymentService);
    when(SpringUtil.getBean(AdapterService.class)).thenReturn(adapterService);

    // Mock data setup
    PaymentNotification mockNotification = new PaymentNotification();
    mockNotification.setNotificationType(EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION);
    mockNotification.setNotificationStatus(EnumPaymentNotificationStatu.WAITING);
    mockNotification.setPaymentId(1L);
    mockNotification.setRetryCount(0);

    Payment mockPayment = new Payment();
    mockPayment.setInstitutionDebtTypeId(1L);
    mockPayment.setCreateDate(LocalDateTime.now());
    mockPayment.setAdditionalInfo1("Info1");

    NotifyPaymentAdapterResponse mockResponse = new NotifyPaymentAdapterResponse();
    mockResponse.setStatus(BillPaymentsConsts.RESPONSE_STATUS.SUCCESS);
    mockResponse.setNotifiedBill(new NotifiedBillAdapterDTO());

    // Mock behavior setup
    when(paymentNotificationService.findPaymentNotificationWithLock(1L)).thenReturn(mockNotification);
    when(paymentService.findPaymentByIdWithLock(1L)).thenReturn(mockPayment);
    when(adapterService.notifyPayment(any(NotifyPaymentAdapterRequest.class), anyString(), anyString())).thenReturn(mockResponse);

    // Test input and execution
    ProcessExecutionInput input = new ProcessExecutionInputConcrete(EnumProcessCode.BILL_PAYMENT);
    input.getDataPack().put(ProcessDataPackKey.PAYMENT_NOTIFICATION_ID.getKey(), 1L);

    process.initProcess(input, new ProcessLogDTO("processLogDto"));
    process.executeProcess();

    // Assertion
    NotifyPaymentProcessOutput output = (NotifyPaymentProcessOutput) process.getExecutionOutput();
    assertNotNull(output); // Ensure output is not null
}
