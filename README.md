@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class RemoteServiceLogWebDTO extends BaseWebDTO {

    private Long id;
    private Long institutionId;
    private String serviceType;
    private String subscriberNo;
    private LocalDate logDate;
    private Long duration;
    private String sendData;
    private String receivedData;
    private String institutionReturnCode;
    private String bankReturnCode;
    private String additionalInfo;
    private Long dataPowerTransactionId;
    private String channelCode;
    private String branchCode;
    private String channelTransactionId;
    private String channelSessionId;
}


This rule raises an issue when a subclass of a class that overrides Object.equals introduces new fields but does not also override the Object.equals method.
