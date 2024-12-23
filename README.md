private List<PaidBillLogDTO> matchBillsWithLogs(List<PaidBillDTO> billDTOS, List<LogRecordDTO> logRecords) {
    List<PaidBillLogDTO> result = new ArrayList<>();

    // LogRecordDTO listesini bir Map'e dönüştürelim (SubscriberNo + Institution + Product anahtar olarak kullanılacak)
    Map<String, LogRecordDTO> logRecordMap = logRecords.stream()
            .collect(Collectors.toMap(
                    log -> log.getSubscriberNo() + "|" + log.getInstitutionCode() + "|" + log.getProductCode(),
                    log -> log
            ));

    // Faturalarla logları eşleştir
    for (PaidBillDTO bill : billDTOS) {
        String key = bill.getSubscriberNo() + "|" + bill.getInstitution() + "|" + bill.getProduct();
        if (logRecordMap.containsKey(key)) {
            LogRecordDTO log = logRecordMap.get(key);
            // Eşleşme durumunda DTO oluştur ve sonuç listesine ekle
            PaidBillLogDTO paidBillLogDTO = new PaidBillLogDTO();
            paidBillLogDTO.setBillNo(bill.getBillNo());
            paidBillLogDTO.setLogDate(log.getLogDate());
            // Diğer gerekli atamalar
            result.add(paidBillLogDTO);
        }
    }

    return result;
}
@Override
public List<PaidBillLogDTO> getPaymentLogsByParameters(RequestPaidBillLogDTO requestDTO) {
    if (requestDTO.getIsMicro()) {
        RequestPaidBillDTO billRequestDTO = mapper.toRequestDTO(requestDTO);
        List<PaidBillDTO> billDTOS = getPaymentsByParameters(billRequestDTO);
        List<LogRecordDTO> logRecords = getFilteredLogRecords(
                requestDTO.getInstitutionCode(),
                requestDTO.getProductCode(),
                requestDTO.getReturnMapCode(),
                requestDTO.getStartDate(),
                requestDTO.getEndDate()
        );

        // Yeni metod ile eşleştirme yap
        return matchBillsWithLogs(billDTOS, logRecords);
    }

    // Harmoni ise doğrudan harmoni servisinden gelen yanıtı return et
    return facade.getPaymentLogsByParameters(requestDTO).getPaidBillLogList();
}
