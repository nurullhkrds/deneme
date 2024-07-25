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
	private ProcessStepHandler stepHandler;

	//Notify process de hata alması durumunda güncelleme yapsın istiyoruz
	protected Boolean shouldRaiseExceptionOnABillError = true;
	
	@Override
	public void beforeExecuteProcess() throws BillException {
		String logPrefix = "\t[PM-BE] ";

		/**
		 * Process kanalda tanımlı mı ?
		 */
		addStepLog(logPrefix + "process channel found checking");
		ProcessUtil.validateConditionWithArgs(processChannel != null, EnumBillResult.PROCESS_CHANNEL_NOT_FOUND,
				channelCode);
		addStepLog(logPrefix + "process channel found checked.");

		/**
		 * process kanalda aktif mi ?
		 */
		addStepLog(logPrefix + "process channel activate checking.");
		ProcessUtil.validateConditionWithArgs(processChannel.getIsActive(), EnumBillResult.PROCESS_CHANNEL_NOT_ACTIVE,
				channelCode);
		addStepLog(logPrefix + "process channel activate checked.");

		/**
		 * process kanal calisma saat aralığı
		 */
		addStepLog(logPrefix + "process channel working time checking.");
		ProcessUtil.validateConditionWithArgs(
				ProcessUtil.isTimeBetweenWorkingHour(LocalTime.now(), processChannel.getWorkingStartTime(),
						processChannel.getWorkingFinishTime()),
				EnumBillResult.PROCESS_CHANNEL_WORKING_TIME_ERROR,
				ProcessUtil.formatWorkingTime(processChannel.getWorkingStartTime()),
				ProcessUtil.formatWorkingTime(processChannel.getWorkingFinishTime()), channelCode);
		addStepLog(logPrefix + "process channel working time checked.");

		/**
		 * Kurum tanımı var mı ?
		 */
		addStepLog(logPrefix + "institution found checking.");
		ProcessUtil.validateCondition(institution != null, EnumBillResult.INSTITUTION_NOT_FOUND);
		addStepLog(logPrefix + "institution found checked.");

		/**
		 * Kurum aktif mi
		 */
		addStepLog(logPrefix + "institution active checking.");
		ProcessUtil.validateCondition(institution.getIsActive(), EnumBillResult.INSTITUTION_NOT_ACTIVE);
		addStepLog(logPrefix + "institution active checked.");

		/**
		 * Kurumun process tanımı var mı ?
		 */
		addStepLog(logPrefix + "institution process found checking.");
		ProcessUtil.validateCondition(institutionProcess != null, EnumBillResult.INSTITUTION_PROCESS_NOT_FOUND);
		addStepLog(logPrefix + "institution process found checked.");

		/**
		 * Kurumun process'i aktif mi ?
		 */
		addStepLog(logPrefix + "institution process activate checking.");
		ProcessUtil.validateCondition(institutionProcess.getIsActive(),
				EnumBillResult.INSTITUTION_PROCESS_NOT_ACTIVE);
		addStepLog(logPrefix + "institution process activate checked.");

		/**
		 * Kurumun kanalında process tanımı var mı ?
		 */
		addStepLog(logPrefix + "institution process channel found checking.");
		ProcessUtil.validateConditionWithArgs(institutionChannelProcess != null,
				EnumBillResult.INSTITUTION_PROCESS_CHANNEL_NOT_FOUND, channelCode);
		addStepLog(logPrefix + "institution process channel found checked.");

		/**
		 * Kurumun kanalında process tanımı aktif mi ?
		 */
		addStepLog(logPrefix + "institution process channel activate checking.");
		ProcessUtil.validateConditionWithArgs(institutionChannelProcess.getIsActive(),
				EnumBillResult.INSTITUTION_PROCESS_CHANNEL_NOT_ACTIVE, channelCode);
		addStepLog(logPrefix + "institution process channel activate checked.");

		/**
		 * Kurumun kanalında tanımlı processin calisma saat araliginda mi ?
		 */
		addStepLog(logPrefix + "institution process channel working time checking.");
		ProcessUtil.validateConditionWithArgs(
				ProcessUtil.isTimeBetweenWorkingHour(LocalTime.now(), institutionChannelProcess.getWorkingStartTime(),
						institutionChannelProcess.getWorkingFinishTime()),
				EnumBillResult.INSTITUTION_PROCESS_CHANNEL_WORKING_TIME_ERROR,
				ProcessUtil.formatWorkingTime(institutionChannelProcess.getWorkingStartTime()),
				ProcessUtil.formatWorkingTime(institutionChannelProcess.getWorkingFinishTime()), channelCode);
		addStepLog(logPrefix + "institution process channel working time checked.");

		/**
		 * Kurumunun kanal tanımı var mı ?
		 */
		addStepLog(logPrefix + "institution channel found checking.");
		ProcessUtil.validateCondition(institutionChannel != null, EnumBillResult.INSTITUTION_CHANNEL_NOT_FOUND);
		addStepLog(logPrefix + "institution channel found checked.");

		/**
		 * Kurumun kanal tanımı aktif mi
		 */
		addStepLog(logPrefix + "institution channel active checking.");
		ProcessUtil.validateCondition(institutionChannel.getIsActive(),
				EnumBillResult.INSTITUTION_CHANNEL_NOT_ACTIVE);
		addStepLog(logPrefix + "institution channel active checked.");

		/**
		 * Kurumun kanal çalışma aralığında mı
		 */
		addStepLog(logPrefix + "institution channel working time checking.");
		ProcessUtil.validateConditionWithArgs(
				ProcessUtil.isTimeBetweenWorkingHour(LocalTime.now(), institutionChannel.getWorkingStartTime(),
						institutionChannel.getWorkingFinishTime()),
				EnumBillResult.INSTITUTION_WORKING_TIME_ERROR,
				ProcessUtil.formatWorkingTime(institutionChannel.getWorkingStartTime()),
				ProcessUtil.formatWorkingTime(institutionChannel.getWorkingFinishTime()), channelCode);
		addStepLog(logPrefix + "institution channel working time checked.");

		/**
		 * Kurum borç tipi tanımlı mı
		 */
		addStepLog(logPrefix + "institution debt type found checking.");
		ProcessUtil.validateCondition(institutionDebtType != null, EnumBillResult.INSTITUTION_DEBT_TYPE_NOT_FOUND);
		addStepLog(logPrefix + "institution debt type found checked.");

		/**
		 * kurum borç tipi aktif mi?
		 */
		addStepLog(logPrefix + "institution debt type active checking.");
		ProcessUtil.validateConditionWithArgs(institutionDebtType.getIsActive(),
				EnumBillResult.INSTITUTION_DEBT_TYPE_NOT_ACTIVE, institutionDebtTypeId);
		addStepLog(logPrefix + "institution debt type active checked.");
	}

	@Override
	public void afterExecuteProcess() throws BillException {
		boolean errorIsNotNullAndNotSuccess = this.error != null && !EnumBillResult.SUCCESS.equals(error);
		if (errorIsNotNullAndNotSuccess && shouldRaiseExceptionOnABillError) {
			ProcessUtil.raiseBillException(error);
		}

		prepareExecutionOutput();
		String responseData1 = logDTO.getResponseData1();
		responseData1 = responseData1.concat("\n------OUTPUT-------\n").concat(executionOutput.toString());
		logDTO.setResponseData1(responseData1);
		logDTO.setResultCode(errorIsNotNullAndNotSuccess ? error.getCode().toString() : EnumBillResult.SUCCESS.getCode().toString());
		logDTO.setResultText(errorIsNotNullAndNotSuccess ? error.getExplanation().toString() : EnumBillResult.SUCCESS.getExplanation());
		logDTO.setReturnType(errorIsNotNullAndNotSuccess ? EnumLoggingResultType.ERROR.getExplanation() : EnumLoggingResultType.SUCCESS.getExplanation());
	}

	@Override
	public void initProcess(ProcessExecutionInput input, ProcessLogDTO logDTO) {
		this.logDTO = logDTO;
		stepHandler = new ProcessStepHandler();

		processCode = input.getProcessCode();
		productCode = input.getProductCode();
		institutionCode = input.getInstitutionCode();

		channelSessionId = input.getChannelSessionId();
		channelTransactionId = input.getChannelTransactionId();
		channelCode = input.getChannelCode();
		agentCode = input.getAgentCode();
		branchCode = input.getBranchCode();
		institutionDebtTypeId = input.getInstitutionDebtTypeId();

		processChannel = processService.getProcessChannel(processCode.getCode(), channelCode);
		institution = processService.getInstitutionForProcess(productCode, institutionCode);


		institutionDebtType = processService.getInstitutionDebtTypeForProcess(productCode, institutionCode,institutionDebtTypeId);
		institutionDebtTypeId = institutionDebtType.getId() ;
		institutionChannel = processService.getInstitutionChannelForProcess(institutionDebtTypeId, channelCode);
		institutionProcess = processService.getInstitutionProcess(productCode, institutionCode, processCode.getCode());
		institutionChannelProcess = processService.getInstitutionChannelProcess(institutionDebtTypeId,
				processCode.getCode(), channelCode);

		// TODO: deep copy olmasına dikkat et
		dataPack = new HashMap<>(input.getDataPack());
		logDTO.setInstitutionId(institution != null ? institution.getId() : null);
		logDTO.setInstitutionDebtTypeId(institutionDebtTypeId != null ? institutionDebtTypeId : null);
	}

	@Override
	public ProcessExecutionOutput getProcessExecutionOutput() throws BillException {
		return this.executionOutput;
	}

	private class ProcessStepHandler {

		private List<ProcessStep> flowList;

		private ProcessStepHandler() {
			this.flowList = new ArrayList<>();
		}

		private void addFlow(ProcessStep step) {
			flowList.add(step);
		}

		private void executeFlow() throws BillException {

			for (ProcessStep step : flowList) {
				if (error == null || error.equals(EnumBillResult.SUCCESS)) {
					addStepLog("\t\t[PM-EP]" + step.getStepName() + " started.");
					step.executeStep();
					addStepLog("\t\t[PM-EP]" + step.getStepName() + " finished.");
				} else {
					addStepLog("\t[PM-EP]-[ERROR_STATE: - "+ error.getCode().toString() + "]"+ step.getStepName());
				}
			}

		}
	}

	private void addStepLog(String log) {
		LogUtil.appendProcessLog(logDTO, log);
	}

	protected void addProcessStep(ProcessStep step) {
		this.stepHandler.addFlow(step);
	}

	protected void executeSteps() throws BillException {
		this.stepHandler.executeFlow();
	}

	protected  boolean isOnlineProcess(){
		return  institutionProcess.getIsOnline();
	}
	
	protected abstract void prepareExecutionOutput();
}
