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

    // Object'ten LocalDate'e dönüşüm için yardımcı metod
    default LocalDate convertToLocalDate(Object date) {
        if (date instanceof Timestamp) {
            return ((Timestamp) date).toLocalDateTime().toLocalDate();
        }
        return null;
    }
