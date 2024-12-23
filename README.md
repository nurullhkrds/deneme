default LogRecordDTO mapToLogRecordDTO(Object[] row) {
    if (row == null) {
        return null;
    }

    LogRecordDTO dto = new LogRecordDTO();
    dto.setSubscriberNo((String) row[0]);
    dto.setLogDate(convertToString(row[1])); // Tarihi olduğu gibi String'e çevir ve set et
    dto.setReceivedData(convertClobToString(row[2])); // CLOB'u String'e çevir
    dto.setSendData(convertClobToString(row[3])); // CLOB'u String'e çevir
    dto.setInstitutionReturnCode((String) row[4]);
    dto.setReturnMapCode((String) row[5]);
    dto.setInstitutionReturnText((String) row[6]);
    dto.setBankReturnCode((String) row[7]);
    dto.setInstitutionCode((String) row[8]);
    dto.setProductCode((String) row[9]);

    return dto;
}

default String convertToString(Object dateObject) {
    if (dateObject instanceof java.sql.Timestamp) {
        return new java.text.SimpleDateFormat("dd.MM.yyyy HH:mm:ss").format(dateObject);
    } else if (dateObject instanceof java.util.Date) {
        return new java.text.SimpleDateFormat("dd.MM.yyyy HH:mm:ss").format(dateObject);
    }
    return dateObject != null ? dateObject.toString() : null;
}

default String convertClobToString(Object clobObject) {
    if (clobObject instanceof java.sql.Clob) {
        try {
            java.sql.Clob clob = (java.sql.Clob) clobObject;
            return clob.getSubString(1, (int) clob.length());
        } catch (Exception e) {
            throw new RuntimeException("Error converting CLOB to String", e);
        }
    } else if (clobObject instanceof String) {
        return (String) clobObject;
    }
    return null;
}
