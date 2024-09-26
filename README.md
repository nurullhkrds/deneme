@Getter
@Setter
public class CreateInstitutionChannelRequest extends BaseCreateWebRequest {

    @NotNull
    @Schema(description = "ID of the institution debt type", example = "1", required = true)
    private Long institutionDebtTypeId;  // InstitutionDebtType ID'si

    @NotNull
    @Schema(description = "Code of the channel", example = "CHANNEL_001", required = true)
    private String channelCode;  // Kanalın kodu

    @NotNull
    @Schema(description = "Is a new bill needed", example = "true", required = true)
    private Boolean isNewBillNeeded;  // Yeni bir fatura gerekli mi?

    @NotNull
    @Schema(description = "Is partial payment allowed", example = "true", required = true)
    private Boolean isPartialPaymentAllowed;  // Kısmi ödeme izinli mi?

    @NotNull
    @Schema(description = "Is overpayment allowed", example = "false", required = true)
    private Boolean isOverPaymentAllowed;  // Fazla ödeme izinli mi?

    @NotNull
    @Schema(description = "Working start time", example = "08:00", required = true)
    private LocalTime workingStartTime;  // Çalışma başlangıç saati

    @NotNull
    @Schema(description = "Working finish time", example = "18:00", required = true)
    private LocalTime workingFinishTime;  // Çalışma bitiş saati

    @NotNull
    @Schema(description = "Is the institution channel active", example = "true", required = true)
    private Boolean isActive;  // Kaydın aktif olup olmadığını belirleyen alan
}



@Getter
@Setter
public class UpdateInstitutionChannelRequest extends BaseUpdateWebRequest {

    @NotNull
    @Schema(description = "ID of the institution channel", example = "1", required = true)
    private Long id;  // Güncellenecek InstitutionChannel kaydının ID'si

    @NotNull
    @Schema(description = "ID of the institution debt type", example = "1", required = true)
    private Long institutionDebtTypeId;  // InstitutionDebtType ID'si

    @NotNull
    @Schema(description = "Code of the channel", example = "CHANNEL_001", required = true)
    private String channelCode;  // Kanalın kodu

    @NotNull
    @Schema(description = "Is a new bill needed", example = "true", required = true)
    private Boolean isNewBillNeeded;  // Yeni bir fatura gerekli mi?

    @NotNull
    @Schema(description = "Is partial payment allowed", example = "true", required = true)
    private Boolean isPartialPaymentAllowed;  // Kısmi ödeme izinli mi?

    @NotNull
    @Schema(description = "Is overpayment allowed", example = "false", required = true)
    private Boolean isOverPaymentAllowed;  // Fazla ödeme izinli mi?

    @NotNull
    @Schema(description = "Working start time", example = "08:00", required = true)
    private LocalTime workingStartTime;  // Çalışma başlangıç saati

    @NotNull
    @Schema(description = "Working finish time", example = "18:00", required = true)
    private LocalTime workingFinishTime;  // Çalışma bitiş saati

    @NotNull
    @Schema(description = "Is the institution channel active", example = "true", required = true)
    private Boolean isActive;  // Kaydın aktif olup olmadığını belirleyen alan
}
