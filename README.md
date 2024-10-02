@JsonCreator
public static EnumExpenseType parse(String value) {
    if (value == null || value.trim().isEmpty()) {
        throw new IllegalArgumentException("EnumExpenseType value cannot be null or empty");
    }

    // Küçük harfe çevirip kontrol et
    EnumExpenseType expenseType = paramaters.get(value.trim().toUpperCase());
    if (expenseType == null) {
        throw new IllegalArgumentException("No enum constant for value: " + value);
    }
    return expenseType;
}
