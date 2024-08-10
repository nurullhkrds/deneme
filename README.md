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
void testInstitutionChannelProcessWorkingTimeError() {
    when(process.institutionChannelProcess.getWorkingStartTime()).thenReturn(LocalTime.of(8, 0));
    when(process.institutionChannelProcess.getWorkingFinishTime()).thenReturn(LocalTime.of(16, 0));

    BillException exception = assertThrows(BillException.class, () -> {
        process.beforeExecuteProcess();
    });

    assertEquals(EnumBillResult.INSTITUTION_PROCESS_CHANNEL_WORKING_TIME_ERROR.getCode(), exception.getResultCode());
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
void testInstitutionChannelWorkingTimeError() {
    when(process.institutionChannel.getWorkingStartTime()).thenReturn(LocalTime.of(8, 0));
    when(process.institutionChannel.getWorkingFinishTime()).thenReturn(LocalTime.of(16, 0));

    BillException exception = assertThrows(BillException.class, () -> {
        process.beforeExecuteProcess();
    });

    assertEquals(EnumBillResult.INSTITUTION_WORKING_TIME_ERROR.getCode(), exception.getResultCode());
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
