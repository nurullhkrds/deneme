lenient().when(getBean(PaymentUtilImpl.class)).thenReturn(paymentUtilImpl);
        lenient().when(getBean(AdapterService.class)).thenReturn(adapterService);
        lenient().when(getBean(ProvisionService.class)).thenReturn(provisionService);
        lenient().when(getBean(InstitutionUserIntService.class)).thenReturn(institutionUserIntService);
        lenient().when(getBean(InstitutionUserIntfMapper.class)).thenReturn(institutionUserIntMapper);
        lenient().when(getBean(PaymentRepository.class)).thenReturn(paymentRepository);
        lenient().when(getBean(BillPaymentRestFacade.class)).thenReturn(billPaymentRestFacade);

        lenient().when(getBean(PaymentMapper.class)).thenReturn(paymentMapper);
        lenient().when(getBean(LimitationService.class)).thenReturn(limitationService);
        lenient().when(getBean(ProcessService.class)).thenReturn(processService);
        lenient().when(getBean(QueryBillsProcess.class)).thenReturn(process);
