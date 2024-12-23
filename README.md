private List<PaidBillLogDTO> matchBillsWithLogs(List<PaidBillDTO> billDTOS, List<LogRecordDTO> logRecords) {
    List<PaidBillLogDTO> result = new ArrayList<>();

    // LogRecordDTO listesini bir Map'e dönüştürelim (SubscriberNo + Institution + Product anahtar olarak kullanılacak)
    Map<String, LogRecordDTO> logRecordMap = logRecords.stream()
            .collect(Collectors.toMap(
                    log -> log.getSubscriberNo() + "|" + log.getInstitutionCode() + "|" + log.getProductCode(),
                    log -> log
            ));

    // Faturalarla logları eşleştir ve PaidBillLogDTO'yu doldur
    for (PaidBillDTO bill : billDTOS) {
        String key = bill.getSubscriberNo() + "|" + bill.getInstitution() + "|" + bill.getProduct();
        if (logRecordMap.containsKey(key)) {
            LogRecordDTO log = logRecordMap.get(key);

            // PaidBillLogDTO oluştur ve alanları doldur
            PaidBillLogDTO paidBillLogDTO = new PaidBillLogDTO();
            paidBillLogDTO.setProduct(bill.getProduct());
            paidBillLogDTO.setInstitution(bill.getInstitution());
            paidBillLogDTO.setLogDate(log.getLogDate().toString());
            paidBillLogDTO.setLogRecordNo(log.getReturnMapCode());
            paidBillLogDTO.setSubscriberNo(bill.getSubscriberNo());
            paidBillLogDTO.setBillNo(bill.getBillNo());
            paidBillLogDTO.setTotalAmount(bill.getTotalAmount());
            paidBillLogDTO.setPaidAmount(bill.getPaidAmount());
            paidBillLogDTO.setDueDate(bill.getDueDate());
            paidBillLogDTO.setPaymentDate(bill.getPaymentDate());
            paidBillLogDTO.setPaymentType(bill.getPaymentType());
            paidBillLogDTO.setReferenceNo(bill.getReferenceNo());
            paidBillLogDTO.setInstitutionReturnCode(log.getInstitutionReturnCode());
            paidBillLogDTO.setInstitutionReturnText(log.getInstitutionReturnText());
            paidBillLogDTO.setProcessed("YES"); // İşlenmiş olarak setle (Örnek)

            result.add(paidBillLogDTO);
        }
    }

    return result;
}
