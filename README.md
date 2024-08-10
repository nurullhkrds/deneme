 @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);

        process.processService = processService;
        process.processChannel = processChannelDTO;
        process.institution = institutionDTO;
        process.institutionDebtType = institutionDebtTypeDTO;
        process.institutionChannel = institutionChannelDTO;
        process.institutionProcess = institutionProcessDTO;
        process.institutionChannelProcess = institutionChannelProcessDTO;
        process.logDTO = processLogDTO;

        // Varsayılan mock davranışlarını ayarla
        when(process.processChannel.getIsActive()).thenReturn(true);
        when(process.processChannel.getWorkingStartTime()).thenReturn(LocalTime.of(0, 0)); // 00:00'da başlasın
        when(process.processChannel.getWorkingFinishTime()).thenReturn(LocalTime.of(23, 59)); // 23:59'da bitsin
        when(process.institution.getIsActive()).thenReturn(true);
        when(process.institutionProcess.getIsActive()).thenReturn(true);
        when(process.institutionChannel.getIsActive()).thenReturn(true);
        when(process.institutionChannel.getWorkingStartTime()).thenReturn(LocalTime.of(0, 0)); // 00:00'da başlasın
        when(process.institutionChannel.getWorkingFinishTime()).thenReturn(LocalTime.of(23, 59)); // 23:59'da bitsin
        when(process.institutionChannelProcess.getIsActive()).thenReturn(true);
        when(process.institutionChannelProcess.getWorkingStartTime()).thenReturn(LocalTime.of(0, 0)); // 00:00'da başlasın
        when(process.institutionChannelProcess.getWorkingFinishTime()).thenReturn(LocalTime.of(23, 59)); // 23:59'da bitsin
        when(process.institutionDebtType.getIsActive()).thenReturn(true);
        when(process.logDTO.getResponseData1()).thenReturn("");
    }

    @Test
    void testInstitutionNotFound() {
        process.institution = null;

        BillException exception = assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });

        assertEquals(EnumBillResult.INSTITUTION_NOT_FOUND.getCode(), exception.getResultCode());
    }

    @Test
    void testInstitutionNotActive() {
        when(process.institution.getIsActive()).thenReturn(false);

        BillException exception = assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });

        assertEquals(EnumBillResult.INSTITUTION_NOT_ACTIVE.getCode(), exception.getResultCode());
    }

    @Test
    void testInstitutionProcessNotFound() {
        process.institutionProcess = null;

        BillException exception = assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });

        assertEquals(EnumBillResult.INSTITUTION_PROCESS_NOT_FOUND.getCode(), exception.getResultCode());
    }

    @Test
    void testInstitutionProcessNotActive() {
        when(process.institutionProcess.getIsActive()).thenReturn(false);

        BillException exception = assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });

        assertEquals(EnumBillResult.INSTITUTION_PROCESS_NOT_ACTIVE.getCode(), exception.getResultCode());
    }

    @Test
    void testInstitutionChannelProcessNotFound() {
        process.institutionChannelProcess = null;

        BillException exception = assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });

        assertEquals(EnumBillResult.INSTITUTION_PROCESS_CHANNEL_NOT_FOUND.getCode(), exception.getResultCode());
    }

    @Test
    void testInstitutionChannelProcessNotActive() {
        when(process.institutionChannelProcess.getIsActive()).thenReturn(false);

        BillException exception = assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });

        assertEquals(EnumBillResult.INSTITUTION_PROCESS_CHANNEL_NOT_ACTIVE.getCode(), exception.getResultCode());
    }

    @Test
    void testInstitutionChannelNotFound() {
        process.institutionChannel = null;

        BillException exception = assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });

        assertEquals(EnumBillResult.INSTITUTION_CHANNEL_NOT_FOUND.getCode(), exception.getResultCode());
    }

    @Test
    void testInstitutionChannelNotActive() {
        when(process.institutionChannel.getIsActive()).thenReturn(false);

        BillException exception = assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });

        assertEquals(EnumBillResult.INSTITUTION_CHANNEL_NOT_ACTIVE.getCode(), exception.getResultCode());
    }

    @Test
    void testInstitutionDebtTypeNotFound() {
        process.institutionDebtType = null;

        BillException exception = assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });

        assertEquals(EnumBillResult.INSTITUTION_DEBT_TYPE_NOT_FOUND.getCode(), exception.getResultCode());
    }

    @Test
    void testInstitutionDebtTypeNotActive() {
        when(process.institutionDebtType.getIsActive()).thenReturn(false);

        BillException exception = assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });

        assertEquals(EnumBillResult.INSTITUTION_DEBT_TYPE_NOT_ACTIVE.getCode(), exception.getResultCode());
    }
}
