public final class BillValidationUtil {

	public static void validateCondition(boolean condition, EnumBillResult error, String appName)
			throws BillException {
		if (!condition) {
			throwBillException(appName, error);
		}
	}

	public static void validateConditionWithArgs(String appName, boolean condition, EnumBillResult error,
			Object... args) throws BillException {
		if (!condition) {
			Long errorCode = error.getCode();
			String errorMessage = error.getExplanation();
			String errorMessageWithArguments = errorMessage;

			if (args != null) {
				for (int i = 0; i < args.length; i++) {
					String searchString = "{" + i + "}";
					errorMessageWithArguments = StringUtils.replace(errorMessageWithArguments, searchString,
							args[i].toString());
				}
			}

			if (CollectionUtils.isEmpty(error.getParameterKey())) {
				throw new BillException(appName,error, errorCode, errorMessageWithArguments);
			}

			else {
				Map<String, String> paramaters = new HashMap<>();
				for (int i = 0; i < error.getParameterKey().size(); i++) {
					paramaters.put(error.getParameterKey().get(i), args[i].toString());
				}
				throw new BillException(appName, error,  errorCode, errorMessageWithArguments, paramaters);

			}

		}
	}
	
	public static void throwBillException(String appName, EnumBillResult billResult) throws BillException {
		throw new BillException(appName, billResult);
	}
	
	public static void throwBillException(String appName, Exception ex) throws BillException {
		throw new BillException(appName, ex);
	}
}
