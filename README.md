public class CreateManagementInstServiceParameterRequest extends BaseCreateWebRequest {

    @NotNull
    @Schema(description = "Parameter Institution Id", example = "1", requiredMode = Schema.RequiredMode.REQUIRED)
    private Long institutionId;

    @NotNull
    @Size(min = 1, max = 50)
    @Schema(description = "Parameter Group Code", example = "VALIDATION", requiredMode = Schema.RequiredMode.REQUIRED)
    private String groupCode;

    @NotNull
    @Size(min = 1, max = 100)
    @Schema(description = "Parameter Key", example = "WORKING_HOURS", requiredMode = Schema.RequiredMode.REQUIRED)
    private String key;

    @NotNull
    @Size(min = 1, max = 300)
    @Schema(description = "Parameter Value", example = "23:50:00-00:05:00", requiredMode = Schema.RequiredMode.REQUIRED)
    private String value;

    @Size(min = 1, max = 300)
    @Schema(description = "Parameter Description", example = "Gün dönümünde bu parametredeki saatlere göre işlemler engellenecek", requiredMode = Schema.RequiredMode.REQUIRED)
    private String description;

    @NotNull
    @Schema(description = "The flag specifying if the parameter will be active or passive", example = "true", requiredMode = Schema.RequiredMode.REQUIRED)
    private Boolean isActive;

}


public class CreateManagementInstServiceParameterRequestDTO extends BaseCreateRequestDTO {

    private Long id;

    private Long institutionId;

    private String groupCode;

    private String key;

    private String value;

    private String description;

    private Boolean isActive;
}


     CreateManagementInstServiceParameterRequestDTO createInstServiceParameterRequestDTO = instServiceParameterWebMapper
                .toCreateInstServiceParameterRequestDTO(createInstServiceParameterRequest);
