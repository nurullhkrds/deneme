if (borcList != null && !borcList.isEmpty()) {
    List<BaseBillDTO> billList = borcList.stream().map(aboneBorc -> {
        BaseBillDTO billDTO = new BaseBillDTO();
        billDTO.setProduct(remoteRequest.getProduct());
        billDTO.setInstitution(remoteRequest.getInstitution());
        billDTO.setBillNo(aboneBorc.getBelgeNo().getValue());
        billDTO.setSubscriberNo(lpadWithZeros(aboneBorc.getAboneNo().toString()));
        billDTO.setBillDueDate(convertToLocalDate(aboneBorc.getSonOdemeTarih()));
        billDTO.setInstitutionServiceType(remoteRequest.getInstitutionServiceType());

        String subscriberName = aboneBorc.getAd().getValue() + " " + aboneBorc.getSoyad().getValue();
        billDTO.setSubscriberName(getAdi(subscriberName));

        BigDecimal calculatedBillAmount = safeGetTutar(aboneBorc.getTutar());
        billDTO.setBillAmount(calculatedBillAmount);
        billDTO.setBillRecalculatedAmount(calculatedBillAmount);

        billDTO.setStatus(EnumBillStatus.NOT_PAID.getValue());
        billDTO.setCurrency(EnumCurrencyCodes.TURKISH_LIRA.getValue());

        setInfoFields(aboneBorc.getAnahtar(), billDTO);
        billDTO.setInfo4(aboneBorc.getBelgeNo().getValue());

        return billDTO;
    }).collect(Collectors.toList());

    response.setBills(billList);
} else {
    response.setStatus(BillPaymentsConsts.RESPONSE_STATUS.SUCCESS);
    response.setInstitutionResultCode(EnumResponseCodes.BILL__NO_BILLS_FOUND.getValue());
    response.setInstitutionResultDetail(EnumResponseCodes.BILL__NO_BILLS_FOUND.getDescription());
}



private BigDecimal safeGetTutar(BigDecimal tutar) {
    return tutar != null ? tutar : BigDecimal.ZERO;
}
