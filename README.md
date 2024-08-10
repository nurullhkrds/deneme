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
