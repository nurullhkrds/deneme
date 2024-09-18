@Getter
@Setter
public class CreateInstitutionFeatureRequest extends BaseCreateWebRequest {
    
    @NotNull
    @Schema(description = "ID of the institution", example = "1", required = true)
    private Long institutionId;  // Institution ID'si

    @NotNull
    @Schema(description = "Code of the feature", example = "FEATURE_001", required = true)
    private String featureCode;  // Feature kodu

    @Size(max = 250)
    @Schema(description = "Value of the feature", example = "Some feature value")
    private String featureValue;  // Feature'ın değeri

    @NotNull
    @Schema(description = "Is the feature active", example = "true", required = true)
    private Boolean isActive;  // Aktif olup olmadığını belirten alan
}
