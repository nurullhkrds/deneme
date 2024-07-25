private List<PaidBillResponseWebDTO> getHarmoniBillList(GetCustomerPaidBillListRequest request) {

		List<PaidBillResponseWebDTO> harmoniBillList = new ArrayList<>();

		ResponseGetCustomerPaidBillList harmoniResponse = billPaymentRestFacade
				.getCustomerPaidBillList(request.getCustomerNo());

		if (SUCCESS.equals(harmoniResponse.getStatus())) {
			List<HmnPaidBillDTO> hmnPaidBillList = harmoniResponse.getBillDTOList();
			harmoniBillList = hmnPaidBillList.stream().map(paymentMapper::toPaidBillResponseWebDTO).toList();
		}

		return harmoniBillList;

	}

	private List<PaidBillResponseWebDTO> getBillList(GetCustomerPaidBillListRequest request) {

		List<PaidBillResponseWebDTO> microBillList;
		List<Payment> customerPaidBillList = paymentRepository.findCustomerPaidBillList(LocalDate.now(),
				request.getCustomerNo(), EnumBillStatu.PAID.getValue(),request.getProductCode());

		// TODO: burada kanal kodu için aynı muhasebe grubundakilerini filtreleyelimm

		ChannelDTO requestChannel = channelService.findChannelByChannelCode(request.getChannelCode());

		microBillList = customerPaidBillList.stream().map(paymentMapper::toDTO)
				.filter(f -> channelService.areChannelsTheSameAccountingGroup(requestChannel,
						channelService.findChannelByChannelCode(f.getChannelCode())))
				.map(bill -> paymentMapper.toPaidBillResponseWebDTO(bill,
						institutionService.getInstitutionById(bill.getInstitutionId())))
				.toList();
		return microBillList;

	}
