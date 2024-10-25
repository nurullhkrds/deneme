if (BillPaymentsConsts.RESPONSE_STATUS.SUCCESS.equals(queryBillResponse.getStatus())) {
    List<BaseBillDTO> billDTOList = kentliBorcBilgileriniGetirResult.value.getBorcBilgisi().stream()
        .flatMap(billInfo -> {
            return (billInfo.getBorcDetaylari().getValue()).getBorcDetayi().stream().map(borcDetayi -> {
                String billNo = generateBillNo(
                    borcDetayi.getSistemId(),
                    borcDetayi.getHesapId(),
                    borcDetayi.getBeyanAnaId(),
                    borcDetayi.getBeyanSiraNo(),
                    borcDetayi.getYil(),
                    borcDetayi.getTaksit(),
                    borcDetayi.getVadeTarihi()
                );

                LocalDate vadeTarihi = convertXMLGregorianCalendartoLocalDate(borcDetayi.getVadeTarihi());

                BaseBillDTO billDTO = new BaseBillDTO();
                billDTO.setProduct(remoteRequest.getProduct());
                billDTO.setInstitution(remoteRequest.getInstitution());
                billDTO.setSubscriberNo(remoteRequest.getSubscriberNumber1());
                billDTO.setBillIssueDate(LocalDate.now());
                billDTO.setCurrency(EnumCurrencyCode.TURKISH_LIRA.getValue());
                billDTO.setBillDueDate(vadeTarihi);
                billDTO.setBillLoadDate(LocalDate.now());
                billDTO.setSubscriberName(billInfo.getAdSoyad().getValue());
                billDTO.setInstitutionServiceType(remoteRequest.getInstitutionServiceType());
                billDTO.setBillAmount(billInfo.getToplamTutar().getValue());
                billDTO.setBillRecalculatedAmount(borcDetayi.getBorcTutari());
                billDTO.setBillNo(billNo);
                billDTO.setInfo1(String.valueOf(borcDetayi.getSistemId()));
                billDTO.setInfo2(String.valueOf(borcDetayi.getHesapId()));
                billDTO.setInfo3(String.valueOf(borcDetayi.getBeyanAnaId()));
                billDTO.setInfo4(String.valueOf(borcDetayi.getBeyanSiraNo()));
                billDTO.setInfo5(String.valueOf(borcDetayi.getYil()));
                billDTO.setInfo6(String.valueOf(borcDetayi.getTaksit()));
                billDTO.setInfo7(String.valueOf(billInfo.getKentliId().getValue()));
                billDTO.setCommissionAmount(borcDetayi.getGecikmeTutari());
                billDTO.setInfo8(remoteRequest.getIdentityNo());

                return billDTO;
            });
        }).toList();

    queryBillResponse.setBills(billDTOList);
}
