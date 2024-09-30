@Getter
@Setter
public class CreateInstitutionChnlPymMethodRequest extends BaseCreateWebRequest {

    @NotNull
    @Schema(description = "ID of the institution channel", example = "52145", required = true)
    private Long institutionChannelId;

    @NotNull
    @Schema(description = "Payment method code", example = "PREPAIDCARD", required = true)
    private EnumPaymentMethod paymentMethod;

    @NotNull
    @Size(max = 50)
    @Schema(description = "Accounting template code", example = "ACC_TEMPLATE_001", required = true)
    private String accountingTemplateCode;

    @NotNull
    @Schema(description = "Number of block days", example = "10", required = true)
    private Integer blockDayCount;

    @NotNull
    @Schema(description = "Block day type", example = "W", required = true)
    private EnumBlockDayType blockDayType;

    @NotNull
    @Schema(description = "Block day strategy code", example = "CHANNEL", required = true)
    private EnumBlockDayStrategyCode blockDayStrategyCode;

    @Schema(description = "Profit share rate", example = "0.05", required = false)
    private BigDecimal profitShareRate;

    @NotNull
    @Schema(description = "Is the feature active", example = "true", required = true)
    private Boolean isActive;
}
