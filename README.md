	private static HmnEventhubDBUtil instance = new HmnEventhubDBUtil();

	public static synchronized HmnEventhubDBUtil getInstance() {
		return instance;
	}
	
	public String create(ProduceEvent event) throws HmnException {
		String globalEventId = null;
		Query insert = getInsertQueryFromSession(HmnProcessUtil.getClientService(), HmnProcessUtil.getClientOperation());
		insert.setString(ProduceEventConstants.GLOBAL_EVENT_ID, event.getGlobalEventId());
		insert.setString(ProduceEventConstants.EVENT_CODE, event.getEventCode());
		insert.setString(ProduceEventConstants.APPLICATION_INSTANCE_ID, event.getApplicationInstanceId());
//		insert.setDate(ProduceEventConstants.PUBLISH_DATE, event.getPublishDate());
		insert.setDate(ProduceEventConstants.SEND_DATE, event.getSendDate());
		insert.setInteger(ProduceEventConstants.STATUS, event.getStatus().getStateNumber());
		insert.setInteger(ProduceEventConstants.TRY_COUNT, event.getTryCount());
		insert.setString(ProduceEventConstants.HEADER, event.getHeader());
		insert.setString(ProduceEventConstants.PAYLOAD, event.getPayload());
		insert.setString(ProduceEventConstants.CONTENT_TYPE, event.getContentType());
		insert.setString(ProduceEventConstants.ERROR_MESSAGE, event.getErrorMessage());
		insert.executeUpdate();
		HmnProcessUtil.getClientSession().flush();
		globalEventId = String.valueOf(event.getGlobalEventId());
		return globalEventId;
	}
