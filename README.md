@Getter
@Setter
public class UpdateReturnMapRequest {
    private Long id;
    private String returnMapCode;
    private String institutionReturnCode;
    private String institutionReturnText;
    private String bankReturnCode;
    private String bankReturnText;
    private EnumReturnType returnType;
    private Boolean isReversible;
}

@Getter
@Setter
public class CreateReturnMapRequest {
    private String returnMapCode;
    private String institutionReturnCode;
    private String institutionReturnText;
    private String bankReturnCode;
    private String bankReturnText;
    private EnumReturnType returnType;
    private Boolean isReversible;
}

