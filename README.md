 @NotNull
    @Schema(description = "ID of the institution channel payment method", example = "52145", required = true)
    private Long institutionChannelPymMethodId;

    @NotNull
    @Schema(description = "Monday block day count", example = "1", required = true)
    private Integer mondayBlockDayCount;

    @NotNull
    @Schema(description = "Tuesday block day count", example = "2", required = true)
    private Integer tuesdayBlockDayCount;

    @Schema(description = "Wednesday block day count", example = "0")
    private Integer wednesdayBlockDayCount;

    @Schema(description = "Thursday block day count", example = "0")
    private Integer thursdayBlockDayCount;

    @Schema(description = "Friday block day count", example = "0")
    private Integer fridayBlockDayCount;

    @Schema(description = "Saturday block day count", example = "0")
    private Integer saturdayBlockDayCount;

    @Schema(description = "Sunday block day count", example = "0")
    private Integer sundayBlockDayCount;
