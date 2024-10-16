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
    @Schema(description = "ID of the institution", example = "1232", requiredMode = Schema.RequiredMode.REQUIRED)
    private LocalTime workingStartTime;


    @NotNull
    @Schema(description = "ID of the institution", example = "1232", requiredMode = Schema.RequiredMode.REQUIRED)
    private LocalTime workingFinishTime;


    @NotNull
    @Schema(description = "ID of the institution", example = "1232", requiredMode = Schema.RequiredMode.REQUIRED)
    private Boolean isActive;


}
