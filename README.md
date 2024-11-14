	@Override
	public String publishGiveOrderEvent(String event) throws HmnServiceException {
		IEventhubAdapterService eventhubAdapter = (IEventhubAdapterService) ServiceRegistry.getInstance().getServiceInstance(INF_EVENTHUB_ADAPTER_SERVICE_NAME);
		JsonObject eventData = eventhubAdapter.createBuilder(EnumEventHubCodes.BILLPAYMENT_GIVEORDER_EVENT.getValue()).build();
		
		String header = eventData.toString();
		String headerWithBody = header.replace("\"PAYLOAD\":{}", "\"PAYLOAD\":" + event);
		
		JsonReader reader = Json.createReader(new StringReader(headerWithBody));

		String globalEventId = eventhubAdapter.createEvent(reader.readObject());
		eventhubAdapter.publishEvent(globalEventId);

		return globalEventId;
	}
