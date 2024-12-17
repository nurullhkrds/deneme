
    @NotNull
    @Schema(description = "ID of the institution channel pym method", example = "52145", requiredMode = Schema.RequiredMode.REQUIRED)
    private Long institutionChannelPymMethodId;

    @NotNull
    @Schema(description = "Collection account number", example = "50623427", requiredMode = Schema.RequiredMode.REQUIRED)
    @Size( max = 9)
    private String collectionAccountNo;

    @NotNull
    @Schema(description = "Institution account number", example = "50623427", requiredMode = Schema.RequiredMode.REQUIRED)
    @Size( max = 9)
    private String institutionAccountNo;

    @NotNull
    @Schema(description = "Currency", example = "YTL", requiredMode = Schema.RequiredMode.REQUIRED)
    @Size( max = 4)
    private String currency;


    @NotNull
    @Schema(description = "Expense type", example = "CUST", requiredMode = Schema.RequiredMode.REQUIRED)
    private EnumExpenseType expenseType;


    @NotNull
    @Schema(description = "Expense account number", example = "50623427", requiredMode = Schema.RequiredMode.REQUIRED)
    @Size( max = 9)
    private String expenseAccountNo;

    @NotNull
    @Schema(description = "Is active", example = "true", requiredMode = Schema.RequiredMode.REQUIRED)
    private Boolean isActive;
