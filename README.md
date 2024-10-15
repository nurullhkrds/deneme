@Setter
@Getter
@NoArgsConstructor
@AllArgsConstructor
@EqualsAndHashCode
public class QueriedBillResponseWebDTO implements Serializable {

    private LocalDate billDueDate;

    private String billNo;

    private String billTerm;

    private BigDecimal billAmount;

    private String currency;

    private String billProvisionId;

    private String explanation;

    private boolean payable;

    private String additionalInfo1;

    private String additionalInfo2;

    private String additionalInfo3;

    private String additionalInfo4;

    private String additionalInfo5;

    private String additionalInfo6;

    private String additionalInfo7;

    private String additionalInfo8;

    private String additionalInfo9;

}
