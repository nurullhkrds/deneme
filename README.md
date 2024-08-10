    @Test
     void testInstitutionNotActive() {
         when(process.processChannel.getIsActive()).thenReturn(true);
         when(process.processChannel.getWorkingStartTime()).thenReturn(LocalTime.of(9, 0));
         when(process.processChannel.getWorkingFinishTime()).thenReturn(LocalTime.of(17, 0));
         when(process.institutionProcess.getIsActive()).thenReturn(true);
         when(process.institutionChannel.getIsActive()).thenReturn(true);
         when(process.institutionChannel.getWorkingStartTime()).thenReturn(LocalTime.of(9, 0));
         when(process.institutionChannel.getWorkingFinishTime()).thenReturn(LocalTime.of(17, 0));
         when(process.institutionChannelProcess.getIsActive()).thenReturn(true);
         when(process.institutionChannelProcess.getWorkingStartTime()).thenReturn(LocalTime.of(9, 0));
         when(process.institutionChannelProcess.getWorkingFinishTime()).thenReturn(LocalTime.of(17, 0));
         when(process.institutionDebtType.getIsActive()).thenReturn(true);
         when(process.logDTO.getResponseData1()).thenReturn("");
         when(process.institution.getIsActive()).thenReturn(false);

         BillException exception = assertThrows(BillException.class, () -> {
             process.beforeExecuteProcess();
         });

         assertEquals(EnumBillResult.INSTITUTION_NOT_ACTIVE.getCode(), exception.getErrorCode());
     }


org.opentest4j.AssertionFailedError: 
Expected :2001
Actual   :1501
