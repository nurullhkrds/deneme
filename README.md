import static org.mockito.Mockito.*;

@Test
void testDoAccounting_BlockDayCount() throws BusinessException, ServiceCallException {
    createAccountingDTO.setDummyMerchant(false);

    // Mock the InstitutionChannelPymMethodDTO
    InstitutionChannelPymMethodDTO mockInstitutionChannelPymMethodDTO = mock(InstitutionChannelPymMethodDTO.class);
    createAccountingDTO.setInstitutionChannelPymMethodDTO(mockInstitutionChannelPymMethodDTO);
    when(mockInstitutionChannelPymMethodDTO.getBlockDayCount()).thenReturn(10);

    CardProvisionResponse cardProvisionResponse = new CardProvisionResponse();
    cardProvisionResponse.setGuid("123456");
    when(cardProvisionService.doProvision(any(CardProvisionRequest.class)))
            .thenReturn(cardProvisionResponse);
    when(accountingUtilServiceImpl.getContractNumber()).thenReturn(Long.valueOf(123456));

    try {
        CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);
        assertTrue(result.isSuccess());
        verify(accountingDateUtil, times(1))
                .getAvailDate(any(EnumBlockDayType.class), anyInt());
    } catch (Exception e) {
        if (e.getCause() != null && e.getCause().getClass().equals(ServiceCallException.class)) {
            Long errorCode = ((ServiceCallException) e.getCause()).getErrorCode();
            assertNotNull(errorCode);
        } else {
            fail("Unexpected exception: " + e.getMessage(), e);
        }
    }
}
