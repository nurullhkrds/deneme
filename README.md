@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
public class InstitutionChnnlPymMthdAccDTO extends UpdatableBaseDTO {

	private Long id;
	private InstitutionChannelPymMethodDTO institutionChannelPymMethod;
	private String collectionAccountNo;
	private String institutionAccountNo;
	private String currency;
	private EnumExpenseType expenseType;
	private String expenseAccountNo;
	private Boolean isActive;

}
