"errorMessage": "class jdk.proxy2.$Proxy390 cannot be cast to class java.lang.String (jdk.proxy2.$Proxy390 is in module jdk.proxy2 of loader 'app'; java.lang.String is in module java.base of loader 'bootstrap')",



@Getter
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


 default LogRecordDTO mapToLogRecordDTO(Object[] row) {
        if (row == null) {
            return null;
        }

        LogRecordDTO dto = new LogRecordDTO();
        dto.setSubscriberNo((String) row[0]);
        dto.setLogDate(convertToLocalDate(row[1]));
        dto.setReceivedData((String) row[2]);
        dto.setSendData((String) row[3]);
        dto.setInstitutionReturnCode((String) row[4]);
        dto.setReturnMapCode((String) row[5]);
        dto.setInstitutionReturnText((String) row[6]);
        dto.setBankReturnCode((String) row[7]);
        dto.setInstitutionCode((String) row[8]);
        dto.setProductCode((String) row[9]);

        return dto;
    }
