	@Override
	public DoBillPaymentResponse doBillPayment(DoBillPaymentRequest doBillPaymentRequest) throws MicroException {

		BillPaymentProcessInput billPaymentProcessInput = processExecutionMapper
				.toBillPaymentProcessInput(doBillPaymentRequest);
		billPaymentProcessInput.setChannelSessionId(requestContext.getChannelSessionId());
		billPaymentProcessInput.setChannelTransactionId(requestContext.getChannelTransactionId());
		BillPaymentProcessOutput billPaymentProcessOutput = (BillPaymentProcessOutput) processManager
				.executeProcess(billPaymentProcessInput);
		DoBillPaymentResponse doBillPaymentResponse = new DoBillPaymentResponse();
		doBillPaymentResponse.setBillId(billPaymentProcessOutput.getBillId());
		doBillPaymentResponse.setContractNumber(billPaymentProcessOutput.getContractNo());
		return doBillPaymentResponse;
	}

	@Override
	public CancelBillPaymentResponse cancelBillPayment(CancelBillPaymentRequest cancelBillPaymentRequest)
			throws MicroException {
		BillPaymentReverseProcessInput billPaymentReverseProcessInput = processExecutionMapper
				.toBillPaymentReverseProcessInput(cancelBillPaymentRequest);
		billPaymentReverseProcessInput.setChannelSessionId(requestContext.getChannelSessionId());
		billPaymentReverseProcessInput.setChannelTransactionId(requestContext.getChannelTransactionId());
		processManager.executeProcess(billPaymentReverseProcessInput);
		CancelBillPaymentResponse cancelBillPaymentResponse = new CancelBillPaymentResponse();
		return cancelBillPaymentResponse;
	}
