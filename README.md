@Getter
@Setter
public class PaidBillLogDTO {

    private String product;

    private String institution;

    private String logDate;

    private String logRecordNo;

    private String subscriberNo;

    private String billNo;

    private BigDecimal totalAmount;

    private BigDecimal paidAmount;

    private String dueDate;

    private String paymentDate;

    private String paymentType;

    private String referenceNo;

    private String institutionReturnCode;

    private String institutionReturnText;

    private String processed;

}


@Setter
@NoArgsConstructor
@AllArgsConstructor
public class LogRecordDTO {

    private String subscriberNo;
    private LocalDate logDate;
    private String receivedData;
    private String sendData;
    private String institutionReturnCode;
    private String returnMapCode;
    private String institutionReturnText;
    private String bankReturnCode;
    private String institutionCode;
    private String productCode;
}
