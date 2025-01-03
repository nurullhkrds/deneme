public String[] convertChannelCodesToPaymentTypes(List<String> channelCodes) {
    if (channelCodes == null || channelCodes.isEmpty()) {
        return new String[0]; // Eğer boşsa boş dizi dön
    }

    return channelCodes.stream()
            .map(EnumOldChannels::parseValue) // Enum eşleşmesini bul
            .filter(channel -> channel != null && channel.getCode() != null) // Null olmayanları al
            .map(EnumOldChannels::getCode) // Code değerini al
            .toArray(String[]::new); // String[] olarak dön
}

@Override
public List<PaidBillLogDTO> getPaymentLogsByParameters(PaidBillLogRequestDTO requestDTO)
        throws MicroException {
    List<PaidBillDTO> billDTOS;
    List<LogRecordDTO> logRecords;

    RequestCheckIsMicro requestCheckIsMicro = createRequest(requestDTO.getInstitution(), requestDTO.getProduct());

    if (facade.checkIsMicroInstitution(requestCheckIsMicro)) {
        RequestPaidBill billRequestDTO = mapper.prepareRequest(requestDTO);
        billDTOS = getPaymentsByParameters(billRequestDTO);
        logRecords = joinAndFilterLogRecords(
                requestDTO.getInstitution(),
                requestDTO.getProduct(),
                requestDTO.getServiceType(),
                requestDTO.getReturnMapCode(),
                requestDTO.getStartDate(),
                requestDTO.getEndDate());

        return matchBillsWithLogs(billDTOS, logRecords);
    }

    // Kanal kodlarını dönüştür
    String[] paymentTypeList = convertChannelCodesToPaymentTypes(requestDTO.getChannelCodes());

    // İkinci request oluştur
    RequestPaidBillLog billLogRequestDTO = mapper.prepareRequestSecond(requestDTO);

    // Dönüşümü ikinci request'e setle
    billLogRequestDTO.setPaymentTypeList(paymentTypeList);

    // Facade çağrısı
    return facade.getPaymentLogsByParameters(billLogRequestDTO).getPaidBillLogList();
}
