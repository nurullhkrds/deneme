return remoteServiceLogs.stream()
                .filter(log -> log.getInstitutionId().equals(institution.getId()))
                .flatMap(log -> returnMaps.stream()
                        .filter(rm -> rm.getInstitutionReturnCode().equals(log.getInstitutionReturnCode())
                                && rm.getBankReturnCode().equals(log.getBankReturnCode()))
                        .map(rm -> new LogRecordDTO(
                                log.getSubscriberNo(),
                                log.getLogDate().toString(),
                                log.getSendData(),
                                log.getInstitutionReturnCode(),
                                rm.getReturnMapCode(),
                                rm.getInstitutionReturnText(),
                                rm.getBankReturnCode(),
                                institution.getInstitutionCode(),
                                institution.getProduct().getCode()
                        ))
                )
                .toList();
    }
