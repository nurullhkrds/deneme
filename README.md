NotBlank
@Size(min = 1, max = 20)
@Pattern(
    regexp = "^[A-Za-z0-9]+$",
    message = "productCode must contain only letters and digits"
)
private String productCode;

@NotBlank
@Size(min = 1, max = 20)
@Pattern(
    regexp = "^[A-Za-z0-9]+$",
    message = "institutionCode must contain only letters and digits"
)
private String institutionCode;

@NotBlank
@Size(min = 1, max = 32)
@Pattern(
    regexp = "^[A-Za-z0-9#/-]+$",
    message = "subscriberNo may contain only letters, digits, #, / and -"
)
private String subscriberNo;
