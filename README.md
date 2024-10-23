 if (BillPaymentsConsts.RESPONSE_STATUS.SUCCESS.equals(queryBillResponse.getStatus())) {
            List<BaseBillDTO> billDTOList = kentliBorcBilgileriniGetirResult.value.getBorcBilgisi().stream()
                    .flatMap(billInfo -> billInfo.getBorcDetaylari().getValue().getBorcDetayi().stream()
                            .map(borcDetayi -> {
                                BaseBillDTO billDTO = new BaseBillDTO();
                                billDTO.setBillNo(String.valueOf(billInfo.getKentliId()));
                                billDTO.setInfo7(String.valueOf(borcDetayi.getYil()));
                                billDTO.setProduct(remoteRequest.getProduct());
                                billDTO.setInstitution(remoteRequest.getInstitution());
                                billDTO.setSubscriberNo(remoteRequest.getSubscriberNumber1());
                                billDTO.setBillIssueDate(LocalDate.now());
                                billDTO.setCurrency(EnumCurrencyCode.TURKISH_LIRA.getValue());
                                billDTO.setBillDueDate(billDTO.getBillDueDate());
                                billDTO.setBillLoadDate(LocalDate.now());
                                billDTO.setSubscriberName(String.valueOf(billInfo.getAdSoyad()));
                                billDTO.setInstitutionServiceType(remoteRequest.getInstitutionServiceType());
                                billDTO.setBillAmount(billInfo.getToplamTutar().getValue());
                                billDTO.setBillRecalculatedAmount(borcDetayi.getBorcTutari());
                                billDTO.setInfo1(String.valueOf(borcDetayi.getHesapId()));
                                billDTO.setInfo2(String.valueOf(borcDetayi.getSistemId()));
                                billDTO.setInfo3(String.valueOf(borcDetayi.getBeyanAnaId()));
                                billDTO.setInfo4(String.valueOf(borcDetayi.getBeyanSiraNo()));
                                billDTO.setCommissionAmount(borcDetayi.getGecikmeTutari());
                                billDTO.setBillIssueDate(convertXMLGregorianCalendartoLocalDate(borcDetayi.getVadeTarihi()));
                                billDTO.setInfo5(String.valueOf(borcDetayi.getTaksit()));
                                return billDTO;
                            })
                    ).toList();

            queryBillResponse.setBills(billDTOList);
