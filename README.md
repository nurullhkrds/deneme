@Mapper(componentModel = "spring")
public interface PaymentMapper {

	PaymentMapper INSTANCE = Mappers.getMapper(PaymentMapper.class);

	@Mapping(source = "paymentTime", target = "paymentTime", dateFormat = "HH:mm:ss")
	Payment toEntity(PaymentDTO dto);

	@Mapping(source = "paymentTime", target = "paymentTime", dateFormat = "HH:mm:ss")
	PaymentDTO toDTO(Payment entity);

	@Mapping(source = "payment.id", target = "id")
	@Mapping(source = "payment.explanation", target = "explanation")
	@Mapping(source = "payment.accountNo", target = "accountNumber")
	@Mapping(source = "payment.amount", target = "billAmount")
	@Mapping(source = "payment.paymentAmount", target = "billRecalculatedAmount")
	@Mapping(source = "payment.creditCardNo", target = "cardNumber")
	@Mapping(source = "payment.contractNo", target = "contractNumber")
	@Mapping(source = "payment.institutionDebtTypeId", target = "debtTypeId")
	@Mapping(source = "institution.product.code", target = "productCode")
	@Mapping(source = "payment.currency.value", target = "currency")
	PaidBillResponseWebDTO toPaidBillResponseWebDTO(PaymentDTO payment, InstitutionDTO institution);

	@Mapping(source = "hmnPaidBill.paymentInformation.balanceAccountPaymentInstrument.accountNumber", target = "accountNumber")
	@Mapping(source = "hmnPaidBill.paymentInformation.creditCardPaymentInstrument.cardNumber", target = "cardNumber")
	@Mapping(source = "hmnPaidBill.paymentInformation.accountingContractNumber", target = "contractNumber")
	@Mapping(source = "product", target = "productCode")
	@Mapping(source = "institution", target = "institutionCode")
	@Mapping(source = "billDueDate", target = "billDueDate", dateFormat = "yyyy-MM-dd")
	PaidBillResponseWebDTO toPaidBillResponseWebDTO(HmnPaidBillDTO hmnPaidBill);

	@Mapping(target = "additionalInfo1", ignore = true)
	@Mapping(target = "additionalInfo2", ignore = true)
	@Mapping(target = "additionalInfo3", ignore = true)
	@Mapping(target = "additionalInfo4", ignore = true)
	@Mapping(target = "additionalInfo5", ignore = true)
	@Mapping(target = "additionalInfo6", ignore = true)
	@Mapping(target = "additionalInfo7", ignore = true)
	@Mapping(target = "additionalInfo8", ignore = true)
	@Mapping(target = "additionalInfo9", ignore = true)
	@Mapping(target = "currency", source = "payment.currency.value")
	PaidBillAdapterDTO toPaidBillAdapterDTO(Payment payment);

	
	
	@Mapping(target = "channelSessionId", source = "paymentCancel.channelSessionId")
	@Mapping(target = "channelTransactionId", source = "paymentCancel.channelTransactionId")
	@Mapping(target = "id", source = "paymentCancel.id")
	@Mapping(target = "branchCode", source = "paymentCancel.branchCode")
	@Mapping(target = "channelCode", source = "paymentCancel.channelCode")
	@Mapping(target = "additionalInfo1", ignore = true)
	@Mapping(target = "additionalInfo2", ignore = true)
	@Mapping(target = "additionalInfo3", ignore = true)
	@Mapping(target = "additionalInfo4", ignore = true)
	@Mapping(target = "additionalInfo5", ignore = true)
	@Mapping(target = "additionalInfo6", ignore = true)
	@Mapping(target = "additionalInfo7", ignore = true)
	@Mapping(target = "additionalInfo8", ignore = true)
	@Mapping(target = "additionalInfo9", ignore = true)
	@Mapping(target = "cancelTime", source = "paymentCancel.createDate", qualifiedByName = "toCancelTime")
	@Mapping(target = "currency", source = "payment.currency.value")
	CancelledBillAdapterDTO toCancelledBillAdapterDTO(Payment payment, PaymentCancelDTO paymentCancel);

	@Named("toCancelTime")
	default String toCancelTime(LocalDateTime createDate) {
		if (createDate == null) {
			return "";
		}

		return createDate.format(DateTimeFormatter.ofPattern("HH:mm:ss"));
	}

	@Mapping(target = "returnCode", source = "result.code")
	@Mapping(target = "returnMessage", source = "result.explanation")
	NotifyPaymentResponse toNotifyPaymentResponse(NotifyPaymentProcessOutput processResult);

	@Mapping(target = "returnCode", source = "result.code")
	@Mapping(target = "returnMessage", source = "result.explanation")
	NotifyPaymentCancelResponse toNotifyPaymentCancelResponse(NotifyPaymentCancelProcessOutput executeProcess);


	@Mapping(target = "billDueDate", source = "paidBillResponseWebDTO.billDueDate", qualifiedByName = "toBillDueDate")
	@Mapping(target = "product", source = "productCode")
	@Mapping(target = "institution", source = "institutionCode")
	@Mapping(target = "bankReferenceNo", source = "contractNumber")
	@Mapping(target = "paymentInformation", source = "paidBillResponseWebDTO", qualifiedByName = "extractPaymentInformation")
	HmnPaidBillDTO toHmnPaidBillDTO(PaidBillResponseWebDTO paidBillResponseWebDTO);

	List<HmnPaidBillDTO> toHmnPaidBillDTOList(List<PaidBillResponseWebDTO> paidBillResponseWebDTO);


	@Named("toBillDueDate")
	default Date toBillDueDate(String billDueDate) {
		return DateUtils.convertLocalDateToDate(DateUtils.parseLocalDate(billDueDate, DateUtils.DATE_FORMAT_YYYY_MM_DD_WITH_HYPHEN));
	}

	@Named("extractPaymentInformation")
	default PaymentInformationDTO extractPaymentInformation(PaidBillResponseWebDTO paidBillResponseWebDTO) {
		PaymentInformationDTO paymentInformationDTO = new PaymentInformationDTO();
		BalanceAccountPaymentInstrumentDTO balanceAccountPaymentInstrument = new BalanceAccountPaymentInstrumentDTO();
		balanceAccountPaymentInstrument.setAccountCurrencyCode(paidBillResponseWebDTO.getCurrency());
		if(paidBillResponseWebDTO.getAccountNumber() != null){
			balanceAccountPaymentInstrument.setAccountNumber(paidBillResponseWebDTO.getAccountNumber());
		}
		paymentInformationDTO.setBalanceAccountPaymentInstrument(balanceAccountPaymentInstrument);

		CreditCardPaymentInstumentDTO creditCardPaymentInstrument = new CreditCardPaymentInstumentDTO();
		creditCardPaymentInstrument.setCurrencyCode(paidBillResponseWebDTO.getCurrency());
		if(paidBillResponseWebDTO.getCardNumber() != null){
			creditCardPaymentInstrument.setCardNumber(paidBillResponseWebDTO.getCardNumber());
		}
		paymentInformationDTO.setCreditCardPaymentInstrument(creditCardPaymentInstrument);
		paymentInformationDTO.setAccountingContractNumber(paidBillResponseWebDTO.getContractNumber());
		return  paymentInformationDTO;
	}


	@Mapping(target = "bankReferenceNo", source = "contractNo")
	@Mapping(target = "paymentInformation", source = "payment", qualifiedByName = "extractPaymentInformationWithPayment")
	@Mapping(target = "commissionAmount", source = "expenseAmount")
	@Mapping(target = "currency",source = "payment.currency.value")
	@Mapping(target = "status",source = "payment.status.hmnValue")
	@Mapping(target = "billAmount",source = "amount")
	@Mapping(target = "billRecalculatedAmount",source = "paymentAmount")

	HmnPaidBillDTO toHmnRecoDetailDTO(Payment payment);

	List<HmnPaidBillDTO> toHmnRecoDetailDTOList(List<Payment> payments);

	@Named("extractPaymentInformationWithPayment")
	default PaymentInformationDTO extractPaymentInformationWithPayment(Payment payment) {
		PaymentInformationDTO paymentInformationDTO = new PaymentInformationDTO();
		paymentInformationDTO.setPaymentDate(DateUtils.convertLocalDateToDate(payment.getPaymentDate()));
		BalanceAccountPaymentInstrumentDTO balanceAccountPaymentInstrument = new BalanceAccountPaymentInstrumentDTO();
		balanceAccountPaymentInstrument.setAccountCurrencyCode(payment.getCurrency().getValue());
		if(payment.getAccountNo() != null){
			paymentInformationDTO.setPaymentSourceCode("H");
			balanceAccountPaymentInstrument.setAccountNumber(payment.getAccountNo());
		}
		paymentInformationDTO.setBalanceAccountPaymentInstrument(balanceAccountPaymentInstrument);

		CreditCardPaymentInstumentDTO creditCardPaymentInstrument = new CreditCardPaymentInstumentDTO();
		creditCardPaymentInstrument.setCurrencyCode(payment.getCurrency().getValue());
		if(payment.getCreditCardNo() != null){
			paymentInformationDTO.setPaymentSourceCode("K");
			creditCardPaymentInstrument.setCardNumber(payment.getCreditCardNo());
		}
		paymentInformationDTO.setCreditCardPaymentInstrument(creditCardPaymentInstrument);
		paymentInformationDTO.setAccountingContractNumber(payment.getContractNo());
		return  paymentInformationDTO;
	}


}
