@Getter
@Setter
@JsonInclude(value = JsonInclude.Include.NON_NULL)
public class RequestPaidBillLog {

    private String requestDate;
    private String reference;
    private String channelCode;
    private String agentCode;
    private String operatingBranchCode;
    private String clientUniqueReference;

    @NotNull
    @Schema(description = "Institution Code", example = "GÜLLÜK", requiredMode = Schema.RequiredMode.REQUIRED)
    private String institution;

    @NotNull
    @Schema(description = "Product Code", example = "su", requiredMode = Schema.RequiredMode.REQUIRED)
    private String product;

    private String [] subscriberList;

    @NotNull
    @Schema(description = "Log Date Start", example = "02.03.2024", requiredMode = Schema.RequiredMode.REQUIRED)
    private Date startDate;

    @NotNull
    @Schema(description = "Log Date Finish", example = "10.03.2024", requiredMode = Schema.RequiredMode.REQUIRED)
    private Date endDate;

    private Date paymentDate;

    private String billNo;

    @NotNull
    @Schema(description = "Payment Type", example = "I,V,L,K", requiredMode = Schema.RequiredMode.REQUIRED)
    private String [] paymentTypeList;

    @NotNull
    @Schema(description = "Process status", example = "E,H", requiredMode = Schema.RequiredMode.REQUIRED)
    private String processed;
}
