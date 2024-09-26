@Getter
@Setter
public class UpdateInstitutionCityRequest extends BaseUpdateWebRequest {

    @NotNull
    @Schema(description = "ID of the institution city record", example = "1", required = true)
    private Long id;  // Güncellenecek InstitutionCity kaydının ID'si

    @NotNull
    @Schema(description = "ID of the institution", example = "1", required = true)
    private Long institutionId;  // Institution ID'si

    @NotNull
    @Schema(description = "City plate code", example = "34", required = true)
    private String cityPlateCode;  // City'nin plaka kodu

    @NotNull
    @Schema(description = "Is the institution city record active", example = "true", required = true)
    private Boolean isActive;  // Kaydın aktif olup olmadığını belirleyen alan
}
