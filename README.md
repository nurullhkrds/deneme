		else {
				Map<String, String> paramaters = new HashMap<>();
				for (int i = 0; i < error.getParameterKey().size(); i++) {
					paramaters.put(error.getParameterKey().get(i), args[i].toString());
				}
				throw new BillException(appName, error,  errorCode, errorMessageWithArguments, paramaters);

			}
