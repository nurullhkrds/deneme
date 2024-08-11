@BeforeEach
void setUp() throws NoSuchFieldException, IllegalAccessException {
    MockitoAnnotations.openMocks(this);
    SpringUtil springUtil = new SpringUtil();

    paymentUtilImpl = new PaymentUtilImpl(institutionFeatureService);
    springUtil.setApplicationContext(applicationContext);

    lenient().when(applicationContext.getBean(PaymentUtilImpl.class)).thenReturn(paymentUtilImpl);
    lenient().when(applicationContext.getBean(PaymentNotificationService.class)).thenReturn(paymentNotificationService);
    lenient().when(applicationContext.getBean(PaymentEventPublisher.class)).thenReturn(paymentEventPublisher);
    lenient().when(applicationContext.getBean(PaymentService.class)).thenReturn(paymentService);
    lenient().when(applicationContext.getBean(AccountingService.class)).thenReturn(accountingService);
    lenient().when(applicationContext.getBean(InstitutionFeatureService.class)).thenReturn(institutionFeatureService);
    lenient().when(applicationContext.getBean(ChannelService.class)).thenReturn(channelService);

    paymentDTO = new PaymentDTO();
    paymentDTO.setId(123L);
    paymentDTO.setChannelCode("someChannelCode");
    paymentDTO.setContractNo(456L);
    paymentDTO.setPaymentMethod(EnumPaymentMethod.ACCOUNT);

    productDTO = new ProductDTO();
    productDTO.setCode("123");
    productDTO.setIsActive(true);

    ProcessLogDTO logDTO = new ProcessLogDTO("123");
    billPaymentReverseProcess.setLogDTO(logDTO);

    channelDTO = new ChannelDTO();
    channelDTO.setCode("123");

    institutionDTO = new InstitutionDTO();
    institutionDTO.setInstitutionCode("123");
    institutionDTO.setIsReverseAllowed(true);
    institutionDTO.setProduct(productDTO);

    // Institution'ı BillPaymentReverseProcess'e set ediyoruz
    billPaymentReverseProcess.setInstitution(institutionDTO);

    when(channelService.findChannelByChannelCode(any())).thenReturn(channelDTO);

    Field stepHandlerField = AbstractProcess.class.getDeclaredField("stepHandler");
    stepHandlerField.setAccessible(true);
    stepHandlerField.set(billPaymentReverseProcess, billPaymentReverseProcess.new ProcessStepHandler());
}
