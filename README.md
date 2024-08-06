rg.mockito.exceptions.misusing.UnnecessaryStubbingException: 
Unnecessary stubbings detected.
Clean & maintainable test code requires zero unnecessary code.
Following stubbings are unnecessary (click to navigate to relevant line of code):
  1. -> at com.ykb.payments.bill.transaction.accounting.provision.service.CardProvisionServiceImplTest.testDoAccounting_SuccessfulDummyMerchant(CardProvisionServiceImplTest.java:173)
Please remove unnecessary stubbings or use 'lenient' strictness. More info: javadoc for UnnecessaryStubbingException class.



    @Test
    void testDoAccounting_SuccessfulDummyMerchant() {
        createAccountingDTO.setDummyMerchant(true);

        MakeProvisionResponse makeProvisionResponse = new MakeProvisionResponse();
        makeProvisionResponse.setSuccess(true);
        makeProvisionResponse.setContractNo(123456L);
        when(provisionNextService.makeProvision(any(MakeProvisionRequest.class)))
                .thenReturn(makeProvisionResponse);

        CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);

        assertFalse(result.isSuccess());
    }

    @Test
    void testDoAccounting_GLAccountingFailure() throws BusinessException, ServiceCallException {
        createAccountingDTO.setDummyMerchant(true);

        MakeProvisionResponse makeProvisionResponse = new MakeProvisionResponse();
        makeProvisionResponse.setSuccess(false);
        makeProvisionResponse.setErrorCode(100L);
        when(provisionNextService.makeProvision(any(MakeProvisionRequest.class)))
                .thenReturn(makeProvisionResponse);

        CreateAccountingResultDTO result = cardProvisionServiceImpl.doAccounting(createAccountingDTO);

        assertFalse(result.isSuccess());
    }
