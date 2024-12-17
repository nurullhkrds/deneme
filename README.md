
@Getter
@Setter
public class UpdateInstitutionChannelProcessRequest extends BaseUpdateWebRequest {


    @NotNull
    @Schema(description = "ID of the institution channel process", example = "150000", requiredMode = Schema.RequiredMode.REQUIRED)
    private Long id;

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
}  @PutMapping("updateInstitutionChannelProcces")
    public ResponseEntity<DataResult<InstitutionChnlProcessWebDTO>> updateInstitutionChannelProcces
            (@RequestBody UpdateInstitutionChannelProcessRequest request) throws MicroException{
        UpdateInstitutionChannelProcessRequestDTO requestDTO=
                institutionChannelProcessMapper.toRequestDTO(request);
        InstitutionChannelProcessDTO dto= institutionChnlProccesService.updateInstitutionChannelProcces(requestDTO);
        InstitutionChnlProcessWebDTO webDTO = institutionChannelProcessMapper.toWebDTO(dto);
        DataResult<InstitutionChnlProcessWebDTO> resultDTO=
                new DataResult<>(ResultConstant.SUCCESSFULLY_UPDATED.getMessage(),webDTO);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }
