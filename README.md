
	@Override
	public void write(JsonWriter out, EnumLoggingResultType value) throws IOException {
		if (value != null) {
			out.jsonValue(value.getValue());
		}

	}

	@Override
	public EnumLoggingResultType read(JsonReader in) throws IOException {
		return EnumLoggingResultType.parse(in.nextString());
	}
