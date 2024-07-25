public Payment findPaymentByIdWithLock(Long paymentId) {
		if (paymentId == null) {
			return null;
		}

		Optional<Payment> findById = paymentRepository.findByIdWithLock(paymentId);

		return findById.isPresent() ? findById.get() : null;
	}

	@Override
	@Transactional
	public void updatePayment(Payment payment) {
		paymentRepository.save(payment);
	}

	@Override
	public PaymentDTO getPayment(Long paymentId) {
		if (paymentId == null) {
			return null;
		}

		Optional<Payment> payment = paymentRepository.findById(paymentId);

		return payment.isPresent() ? paymentMapper.toDTO(payment.get()) : null;
	}

	@Override
	@Transactional(rollbackFor = Exception.class)
	public NotifyPaymentProcessOutput notifyPayment(PaymentNotificationEvent event) throws MicroException {
		NotifyPaymentProcessInput processInput = new NotifyPaymentProcessInput();
		processInput.setAgentCode("SYSTEM");
		processInput.setBranchCode("925");
		processInput.setChannelCode(EnumChannel.SYSTEM.getValue());
		processInput.setChannelSessionId(event.getChannelSessionId());
		processInput.setChannelTransactionId(event.getChannelTransactionId());
		processInput.setProductCode(event.getProductCode());
		processInput.setInstitutionCode(event.getInstitutionCode());
		processInput.setPaymentNotificationId(event.getPaymentNotificationId());

		return (NotifyPaymentProcessOutput) processManager.executeProcess(processInput);
	}

	@Override
	public PaymentCancelDTO getPaymentCancel(Long paymentId) {
		if (paymentId == null) {
			return null;
		}
		Optional<PaymentCancel> paymentCancel = paymentCancelRepository.findByPaymentId(paymentId);
		return paymentCancel.isPresent() ? paymentCancelMapper.toDTO(paymentCancel.get()) : null;
	}

	@Override
	@Transactional(rollbackFor = Exception.class)
	public NotifyPaymentCancelProcessOutput notifyPaymentCancel(PaymentCancelNotificationEvent event) throws MicroException {
		NotifyPaymentCancelProcessInput processInput = new NotifyPaymentCancelProcessInput();
		processInput.setAgentCode("SYSTEM");
		processInput.setBranchCode("925");
		processInput.setChannelCode(EnumChannel.SYSTEM.getValue());
		processInput.setChannelSessionId(event.getChannelSessionId());
		processInput.setChannelTransactionId(event.getChannelTransactionId());
		processInput.setProductCode(event.getProductCode());
		processInput.setInstitutionCode(event.getInstitutionCode());
		processInput.setPaymentNotificationId(event.getPaymentNotificationId());
		
		return (NotifyPaymentCancelProcessOutput) processManager.executeProcess(processInput);
	}

	@Override
	@Transactional(rollbackFor = Exception.class)
	public NotifyPaymentResponse notifyPayment(NotifyPaymentRequest request) throws MicroException {
		
		NotifyPaymentProcessInput processInput = new NotifyPaymentProcessInput();
		processInput.setAgentCode(request.getAgentCode());
		processInput.setBranchCode(request.getOperatingBranchCode());
		processInput.setChannelCode(request.getChannelCode());
		processInput.setChannelSessionId(requestContext.getChannelSessionId());
		processInput.setChannelTransactionId(requestContext.getChannelTransactionId());
		processInput.setProductCode(request.getProductCode());
		processInput.setInstitutionCode(request.getInstitutionCode());
		processInput.setPaymentNotificationId(request.getPaymentNotificationId());

		NotifyPaymentProcessOutput executeProcess = (NotifyPaymentProcessOutput) processManager.executeProcess(processInput);
		
		return paymentMapper.toNotifyPaymentResponse(executeProcess);
	}

	@Override
	@Transactional(rollbackFor = Exception.class)
	public NotifyPaymentCancelResponse notifyPaymentCancel(NotifyPaymentCancelRequest request) throws MicroException {
		NotifyPaymentCancelProcessInput processInput = new NotifyPaymentCancelProcessInput();
		processInput.setAgentCode(request.getAgentCode());
		processInput.setBranchCode(request.getOperatingBranchCode());
		processInput.setChannelCode(request.getChannelCode());
		processInput.setChannelSessionId(requestContext.getChannelSessionId());
		processInput.setChannelTransactionId(requestContext.getChannelTransactionId());
		processInput.setProductCode(request.getProductCode());
		processInput.setInstitutionCode(request.getInstitutionCode());
		processInput.setPaymentNotificationId(request.getPaymentNotificationId());
		
		NotifyPaymentCancelProcessOutput executeProcess = (NotifyPaymentCancelProcessOutput) processManager.executeProcess(processInput);
		
		return paymentMapper.toNotifyPaymentCancelResponse(executeProcess);
	}

	@Override
	public List<HmnPaidBillDTO> getMicroBillList(GetCustomerPaidBillListRequest request) throws MicroException{
		List<PaidBillResponseWebDTO> paidBillResponseWebDTOList = getBillList(request);
		return paymentMapper.toHmnPaidBillDTOList(paidBillResponseWebDTOList);
	}

	@Override
	public CountDTO getReconCount(boolean isPayment,
								  Date reconciliationDate,
								  String productCode,
								  String institutionCode) throws MicroException {
		InstitutionDTO institutionDTO = institutionService.getInstitution(productCode,institutionCode);
		return paymentRepository.getReconCount(institutionDTO.getId(),reconciliationDate,isPayment ? EnumBillStatu.PAID.getValue() :EnumBillStatu.CANCELLED.getValue() );
	}

	@Override
	public List<HmnPaidBillDTO> getReconDetail(boolean isPayment, Date reconciliationDate, String productCode, String institutionCode) throws MicroException {
		InstitutionDTO institutionDTO =institutionService.getInstitution(productCode,institutionCode);
		List<Payment> payments = paymentRepository.getReconDetail(institutionDTO.getId(),reconciliationDate,isPayment ? EnumBillStatu.PAID.getValue() :EnumBillStatu.CANCELLED.getValue() );
		return paymentMapper.toHmnRecoDetailDTOList(payments);
	}
