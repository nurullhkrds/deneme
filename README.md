@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
@EqualsAndHashCode
public class InstitutionUserIntfWebDTO implements Serializable {
	private Long id;
	private String label;
	private String labelDescription;
	private Boolean isNumeric;
	private Integer maxLength;
	private Integer minLength;
	private Boolean completeLengthFlag;
	private Integer screenOrderNo;
	private EnumInterfaceType interfaceType;
	private String explanation;
	private Set<InstUserIntfSubtypeWebDTO> multiOptionList;
}


@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
@EqualsAndHashCode
public class InstitutionDebtTypeWebDTO implements Serializable {
	private Long id;
	private String debtType;
	private String explanation;
	private Set<InstitutionUserIntfWebDTO> institutionUserIntfList;
	private Boolean isPartialPaymentAllowed;
	private Boolean isOverPaymentAllowed;
	private LocalTime workingStartTime;
	private LocalTime workingFinishTime;
	private Boolean isCurrencySelectionAllowed;
	private List<String> paymentMethodList;
	private List<String> accountingCurrencyList;
}

@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
@EqualsAndHashCode
public class InstUserIntfSubtypeWebDTO implements Serializable {
	private String key;
	private String value;	
}


@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
@EqualsAndHashCode
public class CityWebDTO implements Comparable<CityWebDTO>, Serializable {
	@EqualsAndHashCode.Include
	private String code;	
	@EqualsAndHashCode.Exclude
	private String name;
	@Override
	public int compareTo(CityWebDTO o) {
		 return Objects.compare(this.code, o.code, String::compareTo);
	}
}
