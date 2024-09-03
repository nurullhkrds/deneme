	@Override
	public EnumLoggingResultType read(JsonReader in) throws IOException {
		return EnumLoggingResultType.parse(in.nextString());
	}
