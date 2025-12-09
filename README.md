@Test
    void testExecuteProcess_AutoReverseWhenRequired() throws MicroException, NoSuchFieldException, IllegalAccessException {

        PaymentNotificationDTO mockNotification = new PaymentNotificationDTO();
        mockNotification.setNotificationType(EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION);
        mockNotification.setNotificationStatus(EnumPaymentNotificationStatu.WAITING);
        mockNotification.setPaymentId(1L);
        mockNotification.setRetryCount(0);

        PaymentDTO mockPayment = new PaymentDTO();
        mockPayment.setId(1L);
        mockPayment.setContractNo(123L);
        mockPayment.setInstitutionDebtTypeId(1L);
        mockPayment.setCreateDate(LocalDateTime.now());
        mockPayment.setPaymentDate(LocalDate.now());
        mockPayment.setChannelTransactionId("transactionId");
        mockPayment.setChannelSessionId("sessionId");

        NotifyPaymentAdapterResponse mockResponse = new NotifyPaymentAdapterResponse();
        mockResponse.setStatus(BillPaymentsConsts.RESPONSE_STATUS.ERROR.getValue());
        mockResponse.setReverseReqired(true);

        InstitutionDebtTypeDTO institutionDebtTypeDTO = new InstitutionDebtTypeDTO();
        institutionDebtTypeDTO.setId(123L);

        ProcessChannelDTO processChannel = new ProcessChannelDTO();
        processChannel.setId(123L);

        ChannelDTO mockChannel = new ChannelDTO();
        mockChannel.setCode("201");

        when(paymentNotificationService.getPaymentNotificationRecordRefreshedCache(1L)).thenReturn(mockNotification);
        when(paymentService.findPaymentByIdWithLock(1L)).thenReturn(mockPayment);
        when(adapterService.notifyPayment(any(NotifyPaymentAdapterRequest.class), anyString(), anyString())).thenReturn(mockResponse);

        ProcessExecutionInput input = new ProcessExecutionInputConcrete(EnumProcessCode.BILL_PAYMENT);
        input.getDataPack().put(ProcessDataPackKey.PAYMENT_NOTIFICATION_ID.getKey(), 1L);
        input.setInstitutionDebtTypeId(12L);
        input.setProductCode("SU");
        input.setInstitutionCode("ABC");
        input.setChannelCode("301");

        process.setChannelDTO(new ChannelDTO());
        process.initProcess(input, new ProcessLogDTO("processLogDto"));
        process.executeProcess();


        verify(paymentNotificationService).updatePaymentNotification(mockNotification);


        Field f = NotifyPaymentProcess.class.getDeclaredField("autoReversed");
        f.setAccessible(true);
        assertTrue((boolean) f.get(process));

    }
