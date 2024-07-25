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
