@Service
@RequiredArgsConstructor
public class PaymentServiceImpl implements PaymentService {
	private final Logger logger = LoggerFactory.getLogger(PaymentServiceImpl.class);

	private static final String SUCCESS = "S";

	private final ProcessManager processManager;
	private final ProcessExecutionMapper processExecutionMapper;
	private final RequestContext requestContext;
	private final PaymentRepository paymentRepository;
	private final PaymentCancelRepository paymentCancelRepository;
	private final PaymentMapper paymentMapper;
	private final PaymentCancelMapper paymentCancelMapper;
	private final InstitutionService institutionService;
	private final BillPaymentRestFacade billPaymentRestFacade;
	private final InstitutionUserIntService institutionUserIntService;
	private final ChannelService channelService;

	@Override
	public QueryBillsResponse queryBills(QueryBillsRequest getQueryBillsRequest) throws MicroException {
		QueryBillProcessInput queryBillProcessInput = processExecutionMapper
				.toQueryBillProcessInput(getQueryBillsRequest);

		// Mapper'a koyamama sebebimiz session olmayan ortamlardan da process
		// başlatalabilir. (Batch ile çalışan)
		queryBillProcessInput.setChannelSessionId(requestContext.getChannelSessionId());
		queryBillProcessInput.setChannelTransactionId(requestContext.getChannelTransactionId());

		QueryBillsProcessOutput queryBillProcessOutput = (QueryBillsProcessOutput) processManager
				.executeProcess(queryBillProcessInput);
		return processExecutionMapper.toQueryBillsResponse(queryBillProcessOutput);
	}

	@Override
	public GetCustomerPaidBillListResponse getCustomerPaidBillList(GetCustomerPaidBillListRequest request)
			throws MicroException {

		List<PaidBillResponseWebDTO> microBillList = getBillList(request);
		List<PaidBillResponseWebDTO> harmoniBillList = getHarmoniBillList(request);
		List<PaidBillResponseWebDTO> combinedBillList = new ArrayList<>();

		combinedBillList.addAll(microBillList);
		combinedBillList.addAll(harmoniBillList);

		BillValidationUtil.validateCondition(!CollectionUtils.isEmpty(combinedBillList),
				EnumBillResult.PAID_BILL_NOT_FOUND_ERROR, BillTransactionConstant.APP_NAME);

		GetCustomerPaidBillListResponse response = new GetCustomerPaidBillListResponse();
		response.setBillList(combinedBillList);

		return response;
	}

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

	@Override
	public ParseSubscriberNoIntoPartsResponse parseSubscriberNoIntoParts(ParseSubscriberNoIntoPartsRequest request) {
		ParseSubscriberNoIntoPartsResponse response = new ParseSubscriberNoIntoPartsResponse();
		List<InstitutionUserIntfDTO> institutionUserIntfDTOList;
		if (request.getDebtTypeID() == null) {
			institutionUserIntfDTOList = institutionUserIntService.getDefaultUserInterface(request.getProductCode(),
					request.getInstitutionCode());
		} else {
			institutionUserIntfDTOList = institutionUserIntService.getUserInterface(request.getDebtTypeID());
		}
		List<SubsrciberNoPartResponseWebDTO> subscriberNoIntoPartList = SubscriberNumberUtils
				.parseSubscriberNoIntoParts(institutionUserIntfDTOList, request.getSubscriberNo());
		response.setSubsrciberNoPartResponseWebDTO(subscriberNoIntoPartList);
		return response;
	}

	@Override
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

}
