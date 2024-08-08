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
	public List<HmnPaidBillDTO> getMicroBillList(GetCustomerPaidBillListRequest request) throws MicroException{
		List<PaidBillResponseWebDTO> paidBillResponseWebDTOList = getBillList(request);
		return paymentMapper.toHmnPaidBillDTOList(paidBillResponseWebDTOList);
	}
