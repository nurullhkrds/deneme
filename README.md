  @Override
    public DataResult<BaseRetrunMapDTO> createBaseReturnMap(CreateBaseReturnMapRequest request) {
        
        return null;
    }

@Getter
@Setter
@JsonIgnoreProperties(ignoreUnknown = true)
public class CreateBaseReturnMapRequest {
    private String returnMapCode;
    private Boolean isActive;

}
