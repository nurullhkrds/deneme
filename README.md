@Getter
@Setter
public class AdminInstitutionChannelProcessRequest extends BaseCreateWebRequest {

    @NotNull
    @Schema(description = "ID of the institution channel", example = "750006", requiredMode = Schema.RequiredMode.REQUIRED)
    private Long institutionChannelId;

    @NotNull
    @Schema(description = "ID of the institution process", example = "500006", requiredMode = Schema.RequiredMode.REQUIRED)
    private Long institutionProcessId;

    @NotNull
    @Schema(description = "Working start time", example = "09:00", requiredMode = Schema.RequiredMode.REQUIRED)
    private LocalTime workingStartTime;

    @NotNull
    @Schema(description = "Working finish time", example = "18:00", requiredMode = Schema.RequiredMode.REQUIRED)
    private LocalTime workingFinishTime;

    @NotNull
    @Schema(description = "Indicates whether the process is active", example = "true", requiredMode = Schema.RequiredMode.REQUIRED)
    private Boolean isActive;
}
