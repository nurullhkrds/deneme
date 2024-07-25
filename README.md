@Override
	public PaymentDTO insertPayment(PaymentDTO paymentDTO) {
		Payment paymentEntity = paymentRepository.save(paymentMapper.toEntity(paymentDTO));
		return paymentMapper.toDTO(paymentEntity);
	}

	@Override
	public PaymentDTO getPayment(Long id, Long contractNo) {
		Optional<Payment> payment = paymentRepository.findByIdAndContractNo(id, contractNo);
		if (payment.isEmpty()) {
			return null;
		}

		return paymentMapper.toDTO(payment.get());
	}

	@Override
	@Transactional
	public void updateStatus(EnumBillStatu status, Long id, Long contractNo) {
		paymentRepository.updateStatus(status.getValue(), id, contractNo);
	}

	@Override
	public PaymentCancelDTO insertPaymentCancel(PaymentCancelDTO paymentCancelDTO) {
		PaymentCancel paymentCancelEntity = paymentCancelRepository
				.save(paymentCancelMapper.toEntity(paymentCancelDTO));
		return paymentCancelMapper.toDTO(paymentCancelEntity);
	}
