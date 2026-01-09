
public class RequestDto {

    @Pattern(
      regexp = "^$|^[0-9a-fA-F]{8}-[0-9a-fA-F]{4}-[1-5][0-9a-fA-F]{3}-[89abAB][0-9a-fA-F]{3}-[0-9a-fA-F]{12}$",
      message = "documentGuid must be null or UUID"
    )
    private String documentGuid;
