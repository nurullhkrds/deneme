public class CreateReturnMapRequest {
    private String bankReturnCode;
    private String bankReturnText;
    private String institutionReturnCode;
    private String institutionReturnText;
    private String returnType;
    private Boolean isReversible;

    // Getters and setters
    public String getBankReturnCode() {
        return bankReturnCode;
    }

    public void setBankReturnCode(String bankReturnCode) {
        this.bankReturnCode = bankReturnCode;
    }

    public String getBankReturnText() {
        return bankReturnText;
    }

    public void setBankReturnText(String bankReturnText) {
        this.bankReturnText = bankReturnText;
    }

    public String getInstitutionReturnCode() {
        return institutionReturnCode;
    }

    public void setInstitutionReturnCode(String institutionReturnCode) {
        this.institutionReturnCode = institutionReturnCode;
    }

    public String getInstitutionReturnText() {
        return institutionReturnText;
    }

    public void setInstitutionReturnText(String institutionReturnText) {
        this.institutionReturnText = institutionReturnText;
    }

    public String getReturnType() {
        return returnType;
    }

    public void setReturnType(String returnType) {
        this.returnType = returnType;
    }

    public Boolean getIsReversible() {
        return isReversible;
    }

    public void setIsReversible(Boolean isReversible) {
        this.isReversible = isReversible;
    }
}
{
    "bankReturnCode": "BR123",
    "bankReturnText": "Bank return text",
    "institutionReturnCode": "IR123",
    "institutionReturnText": "Institution return text",
    "returnType": "SomeType",
    "isReversible": true
}
@PostMapping("/createReturnMap")
public ResponseEntity<DataResult<ReturnMap>> createReturnMap(@RequestBody CreateReturnMapRequest createReturnMapRequest){
    // Log the request data
    System.out.println(createReturnMapRequest);
    
    DataResult<ReturnMap> result = returnMapService.createReturnMap(createReturnMapRequest);
    return ResponseEntity.status(result.getStatusCode()).body(result);
}
