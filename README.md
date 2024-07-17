createReverseAccountingResultDTO.setSuccess(true);
		}catch (Exception e){
			if(e.getCause()!=null && e.getCause().getClass().equals(ServiceCallException.class)){
				Long errorCode =((ServiceCallException) e.getCause()).getErrorCode();
				handleException(errorCode, createReverseAccountingResultDTO);
				return createReverseAccountingResultDTO;
			}
			createReverseAccountingResultDTO.setError(EnumBillResult.GENERIC_UNKNOWN_ERROR);
			createReverseAccountingResultDTO.setSuccess(false);
		}

		return createReverseAccountingResultDTO;
	}
