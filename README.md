@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class AdapterInstitutionDTO {

    private Long id;

    private Product product;

    private String institutionCode;

    private Long customerNo;

    private String name;

    private String explanation;

    private OwnerDepartment ownerDepartment;

    private LocalDate protocolStartDate;

    private LocalDate protocolEndDate;

    private Boolean isReverseAllowed;

    private Boolean isOrderAllowed;

    private Boolean hasDebtType;

    private String iconText;

    private Boolean isActive;
}
