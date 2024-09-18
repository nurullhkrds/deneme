@Getter
@Setter
public class CreateInstitutionDebtTypeRequest extends BaseCreateWebRequest {

    @NotNull
    @Schema(description = "ID of the institution", example = "1", required = true)
    private Long institutionId;  // Institution ID'si

    @NotNull
    @Size(max = 30)
    @Schema(description = "Debt type of the institution", example = "LOAN", required = true)
    private String debtType;  // Debt type

    @NotNull
    @Size(max = 500)
    @Schema(description = "Explanation for the debt type", example = "This is a loan debt", required = true)
    private String explanation;  // Debt explanation

    @NotNull
    @Schema(description = "Is the debt type active", example = "true", required = true)
    private Boolean isActive;  // Aktif olup olmadığını belirten alan
}


@Getter
@Setter
public class UpdateInstitutionDebtTypeRequest extends BaseUpdateWebRequest {

    @NotNull
    @Schema(description = "ID of the institution debt type", example = "1", required = true)
    private Long id;  // Güncellenecek debt type kaydının ID'si

    @NotNull
    @Schema(description = "ID of the institution", example = "1", required = true)
    private Long institutionId;  // Institution ID'si

    @NotNull
    @Size(max = 30)
    @Schema(description = "Debt type of the institution", example = "LOAN", required = true)
    private String debtType;  // Debt type

    @NotNull
    @Size(max = 500)
    @Schema(description = "Explanation for the debt type", example = "This is a loan debt", required = true)
    private String explanation;  // Debt explanation

    @NotNull
    @Schema(description = "Is the debt type active", example = "true", required = true)
    private Boolean isActive;  // Aktif olup olmadığını belirten alan
}
