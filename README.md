    private final static String OPERATION_TYPE="Hepsi";
Reorder the modifiers to comply with the Java Language Specification.


    private static final DateTimeFormatter DATE_FORMATTER = DateTimeFormatter.ofPattern("dd.MM.yyyy");
private static final String OPERATION_TYPE = "Hepsi";


        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd.MM.yyyy");
Define a constant instead of duplicating this literal "dd.MM.yyyy" 8 times.
