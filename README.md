public List<CustomResponse> joinAndFilterData(String institutionCode, String productCode, String returnMapCode, LocalDate startDate, LocalDate endDate) {
    List<RemoteServiceLog> remoteServiceLogs = remoteServiceLogService.findLogsByCriteria(institutionCode, productCode, returnMapCode, startDate, endDate);
    Institution institution = institutionService.findByInstitutionCodeAndProductCode(institutionCode, productCode);
    List<ReturnMap> returnMaps = returnMapService.findByReturnMapCode(returnMapCode);

    return remoteServiceLogs.stream()
        .filter(log -> log.getInstitutionId().equals(institution.getId()))
        .flatMap(log -> returnMaps.stream()
            .filter(rm -> rm.getInstitutionReturnCode().equals(log.getInstitutionReturnCode())
                        && rm.getBankReturnCode().equals(log.getBankReturnCode()))
            .map(rm -> new CustomResponse(
                log.getSubscriberNo(),
                log.getLogDate().toString(),
                log.getSendData(),
                log.getInstitutionReturnCode(),
                rm.getReturnMapCode(),
                rm.getInstitutionReturnText(),
                rm.getBankReturnCode(),
                institution.getInstitutionCode(),
                institution.getProductCode()
            ))
        )
        .collect(Collectors.toList());
}
