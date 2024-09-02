	if (StringUtils.isNotBlank(responseData) && responseData.length() > LoggingConstants.MAX_LOGGING_LENGHT) {

			String responseData1 = StringUtils.substring(responseData, 0, LoggingConstants.MAX_LOGGING_LENGHT);
			String responseData2 = StringUtils.substring(responseData, LoggingConstants.MAX_LOGGING_LENGHT);
			responseData2 = responseData2.length() > LoggingConstants.MAX_LOGGING_LENGHT
					? StringUtils.substring(responseData2, 0, LoggingConstants.MAX_LOGGING_LENGHT)
					: responseData2;
			processLog.setResponseData1(responseData1);
			processLog.setResponseData2(responseData2);
		}
