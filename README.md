@Getter
@Setter
public abstract class AbstractProcess implements IProcess {

	protected ProcessExecutionOutput executionOutput;
	protected ProcessChannelDTO processChannel;
	protected ProcessLogDTO logDTO;
	protected InstitutionDTO institution;
	protected InstitutionDebtTypeDTO institutionDebtType;
	protected InstitutionChannelDTO institutionChannel;
	protected InstitutionProcessDTO institutionProcess;
	protected InstitutionChannelProcessDTO institutionChannelProcess;

	protected String channelSessionId;
	protected String channelTransactionId;

	protected String channelCode;
	protected String agentCode;
	protected String branchCode;

	protected String productCode;
	protected String institutionCode;
	// Null gelmesi durumunda default debt type olcak.
	protected Long institutionDebtTypeId;

	protected ProcessService processService;
	private EnumProcessCode processCode;
	protected EnumBillResult error = null;

	protected Map<String, Object> dataPack;
