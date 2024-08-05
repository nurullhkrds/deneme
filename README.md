
    @NotBlank(message = "product code is required")
    @Schema(description = "product code", requiredMode = Schema.RequiredMode.REQUIRED)
    private String productCode;

    @NotBlank(message = "institution code is required")
    @Schema(description = "institution code", requiredMode = Schema.RequiredMode.REQUIRED)
    private String institutionCode;

    @NotBlank(message = "debtTypeID is not required")
    @Schema(description = "debtTypeID", requiredMode = Schema.RequiredMode.NOT_REQUIRED)
    private String debtTypeID;

    @NotBlank(message = "bill provision id is required")
    @Schema(description = "bill provision id", requiredMode = Schema.RequiredMode.REQUIRED)
    private String billProvisionId;

    @NotBlank(message = "payment amount is required")
    @Schema(description = "payment amount", requiredMode = Schema.RequiredMode.REQUIRED)
    private BigDecimal paymentAmount;

    @NotBlank(message = "currency is required")
    @Schema(description = "currency", requiredMode = Schema.RequiredMode.REQUIRED)
    private String currency;

    @NotBlank(message = "payment method type is required")
    @Schema(description = "payment method type", requiredMode = Schema.RequiredMode.REQUIRED)
    private String paymentMethodType;

    @NotBlank(message = "account payment method detail is required")
    @Schema(description = "account payment method detail", requiredMode = Schema.RequiredMode.NOT_REQUIRED)
    private AccountPaymentMethodDetailWebDTO accountPaymentMethodDetail;

    @NotBlank(message = "credit card payment method detail is required")
    @Schema(description = "credit card payment method detail", requiredMode = Schema.RequiredMode.NOT_REQUIRED)
    private CreditCardPaymentMethodDetailWebDTO creditCardPaymentMethodDetail;

    @NotBlank(message = "cash payment method detail is required")
    @Schema(description = "cash payment method detail", requiredMode = Schema.RequiredMode.NOT_REQUIRED)
    private CashPaymentMethodDetailWebDTO cashPaymentMethodDetail;
