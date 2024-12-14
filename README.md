if (borcList != null && !borcList.isEmpty()) {
    List<BaseBillDTO> billList = borcList.stream().map(aboneBorc -> {
        BaseBillDTO billDTO = new BaseBillDTO();
        billDTO.setProduct(remoteRequest.getProduct());
        billDTO.setInstitution(remoteRequest.getInstitution());
        billDTO.setBillNo(aboneBorc.getBelgeNo().getValue());
        billDTO.setSubscriberNo(remoteRequest.getSubscriberNumber1());
        billDTO.setSubscriberNoPart1(remoteRequest.getSubscriberNumber1());
        billDTO.setSubscriberNoPart2(remoteRequest.getSubscriberNumber2());
        billDTO.setSubscriberNoPart3(remoteRequest.getSubscriberNumber3());
        billDTO.setBillDueDate(convertToLocalDate(aboneBorc.getSonOdemeTarih()));
        billDTO.setInstitutionServiceType(remoteRequest.getInstitutionServiceType());
        
        String subscriberName = aboneBorc.getAd().getValue() + " " + aboneBorc.getSoyad().getValue();
        billDTO.setSubscriberName(getAdi(subscriberName));
        
        BigDecimal calculatedBillAmount = aboneBorc.getTutar() != null ? aboneBorc.getTutar() : BigDecimal.ZERO;
        billDTO.setBillAmount(calculatedBillAmount);
        billDTO.setBillRecalculatedAmount(calculatedBillAmount);
        billDTO.setStatus(EnumBillStatus.NOT_PAID.getValue());
        billDTO.setCurrency(remoteRequest.getCurrencyCode());
        
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
