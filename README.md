java: Can't generate mapping method from iterable type from java stdlib to non-iterable type.
 @Mapping(target = "subscriberNo", expression = "java((String) row[0])")
    @Mapping(target = "logDate", expression = "java(convertToLocalDate(row[1]))")
    @Mapping(target = "receivedData", expression = "java((String) row[2])")
    @Mapping(target = "sendData", expression = "java((String) row[3])")
    @Mapping(target = "institutionReturnCode", expression = "java((String) row[4])")
    @Mapping(target = "returnMapCode", expression = "java((String) row[5])")
    @Mapping(target = "institutionReturnText", expression = "java((String) row[6])")
    @Mapping(target = "bankReturnCode", expression = "java((String) row[7])")
    @Mapping(target = "institutionCode", expression = "java((String) row[8])")
    @Mapping(target = "productCode", expression = "java((String) row[9])")
    LogRecordDTO mapToLogRecordDTO(Object[] row);

    default LocalDate convertToLocalDate(Object date) {
        if (date instanceof Timestamp) {
            return ((Timestamp) date).toLocalDateTime().toLocalDate();
        }
        return null;
    }
