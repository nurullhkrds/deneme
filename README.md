
@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
@EqualsAndHashCode
public class InstitutionPymMethodWebDTO {
	private String expenseCode;
	private String expenseType;
	private String expenseAccountNo;
}
@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
@EqualsAndHashCode
public class InstUserIntfSubtypeWebDTO {
	private String key;
	private String value;	
}

@Data
@AllArgsConstructor
@NoArgsConstructor
public class CountDTO {
    private Integer totalCount = 0;
    private BigDecimal totalAmount = BigDecimal.ZERO;
}
