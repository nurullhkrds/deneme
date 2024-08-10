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
