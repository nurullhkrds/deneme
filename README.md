@AllArgsConstructor
@JsonAdapter(EnumAccountingSourceConverter.class)
@ToString
public enum EnumAccountingSource {

	PAYMENT("PAYMENT", "Muhasabe biz tarafından gerceklesmis"),
	MERCHANT("MERCHANT", "Muhasebe üye iş yeri ekibi tarafından gerçekleşmiş");

	@Getter
	@JsonValue
	private final String value;
	@Getter
	@JsonValue
	private final String description;

	private static final Map<String, EnumAccountingSource> paramaters;

	static {
		paramaters = new LinkedHashMap<>();

		for (EnumAccountingSource each : EnumAccountingSource.values()) {
			paramaters.put(each.value, each);
		}

	}

	@JsonCreator
	public static EnumAccountingSource parse(String value) {
		return paramaters.get(value);
	}
}
