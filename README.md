lenient().when(applicationContext.getBean(PaymentNotificationService.class)).thenReturn(paymentNotificationService);
lenient().when(applicationContext.getBean(PaymentEventPublisher.class)).thenReturn(paymentEventPublisher);
lenient().when(applicationContext.getBean(PaymentUtilImpl.class)).thenReturn(paymentUtilImpl);
lenient().when(applicationContext.getBean(PaymentService.class)).thenReturn(paymentService);
lenient().when(applicationContext.getBean(AccountingService.class)).thenReturn(accountingService);
lenient().when(applicationContext.getBean(InstitutionFeatureService.class)).thenReturn(institutionFeatureService);
lenient().when(applicationContext.getBean(ChannelService.class)).thenReturn(channelService);
