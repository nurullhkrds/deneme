return remoteServiceLogs.stream()
    .filter(log -> log.getInstitutionId().equals(institution.getId()))
    .map(log -> new LogRecordDTO(
            log.getSubscriberNo(),
            log.getLogDate().toString(),
            log.getSendData(),
            log.getInstitutionReturnCode(),
            "DefaultReturnMapCode", // Bu alan için varsayılan bir değer
            "DefaultInstitutionReturnText", // Bu alan için varsayılan bir değer
            log.getBankReturnCode(),
            institution.getInstitutionCode(),
            institution.getProduct().getCode()
    ))
    .toList();
