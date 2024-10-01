{
    "exceptionData": {
        "applicationName": "PAYMENTS.BILL.bill-transaction",
        "errorCode": -995,
        "errorMessage": "Invalid request: JSON parse error: Input mismatch reading Enum `com.ykb.payments.bill.transaction.institution.enums.EnumBlockDayType`: properties-based `@JsonCreator` ([method com.ykb.payments.bill.transaction.institution.enums.EnumBlockDayType#parse(java.lang.String)]) expects JSON Object (JsonToken.START_OBJECT), got JsonToken.VALUE_STRING; nested exception is com.fasterxml.jackson.databind.exc.MismatchedInputException: Input mismatch reading Enum `com.ykb.payments.bill.transaction.institution.enums.EnumBlockDayType`: properties-based `@JsonCreator` ([method com.ykb.payments.bill.transaction.institution.enums.EnumBlockDayType#parse(java.lang.String)]) expects JSON Object (JsonToken.START_OBJECT), got JsonToken.VALUE_STRING\n at [Source: (org.springframework.util.StreamUtils$NonClosingInputStream); line: 7, column: 19] (through reference chain: com.ykb.payments.bill.transaction.institution.admin.web.request.create.CreateInstitutionChnlPymMethodRequest[\"blockDayType\"])",
        "traceId": null
    },
    "parameters": {}
}



{
  "createUser": "U099999",
  "institutionChannelId": 52145,
  "paymentMethod": "PREPAIDCARD",
  "accountingTemplateCode": "ACC_TEMPLATE_001",
  "blockDayCount": 10,
  "blockDayType": "CALENDAR_DAY",
  "blockDayStrategyCode": "CHANNEL",
  "profitShareRate": 0.05,
  "isActive": true
}


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

    @Schema(description = "Profit share rate", example = "0.05", required = true)
    private BigDecimal profitShareRate;

    @NotNull
    @Schema(description = "Is the feature active", example = "true", required = true)
    private Boolean isActive;
}
