@Mapper(componentModel = "spring")
public interface BillLogMapper {


    RequestPaidBillLogDTO toRequestDTO(PaidBillLogRequest request);

}



public class ResponsePaidBillLog {

    private String responseDate;
    private String reference;
    private String status;
    private ResponseStatusMsgDTO statusMessage;
    private String clientUniqueReference;
    private List<PaidBillLogDTO> paidBillLogList;

}

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






@Getter
@Setter
public class PaidBillLogWebDTO {

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
