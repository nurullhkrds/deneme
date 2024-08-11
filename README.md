@BeforeEach
void setUp() throws NoSuchFieldException, IllegalAccessException {
    MockitoAnnotations.openMocks(this);
    SpringUtil.setApplicationContext(applicationContext); // ApplicationContext'i burada ayarlayın

    lenient().when(getBean(PaymentUtilImpl.class)).thenReturn(paymentUtilImpl);
    // Diğer lenient ayarları...

    institution = new InstitutionDTO();
    institution.setId(123L);
    institution.setIsReverseAllowed(true);
    institution.setInstitutionCode("123");

    institutionDebtType = new InstitutionDebtTypeDTO();
    institutionDebtType.setId(123L);
    institutionDebtType.setIsActive(true);
    institutionDebtType.setDebtType("debt");

    institutionProcess = new InstitutionProcessDTO();
    institutionProcess.setId(123L);

    ProcessLogDTO logDTO = new ProcessLogDTO("123");
    process.setLogDTO(logDTO);

    process.setInstitution(institution);

    Field stepHandlerField = AbstractProcess.class.getDeclaredField("stepHandler");
    stepHandlerField.setAccessible(true);
    stepHandlerField.set(process, process.new ProcessStepHandler());
}
@Test
void testExecuteProcess_Success() throws BillException {
    when(paymentUtilImpl.isFomOperationEnabled(any(InstitutionDTO.class))).thenReturn(true);

    process.setDataPack(new HashMap<>());
    process.getDataPack().put(ProcessDataPackKey.BILL_ID.getKey(), 1L);
    process.getDataPack().put(ProcessDataPackKey.CONTRACT_NO.getKey(), 456L);

    process.executeProcess();

    verify(paymentEventPublisher).findPublishPaymentCancelEvent(any());

    assertNull(process.getExecutionOutput());
}
