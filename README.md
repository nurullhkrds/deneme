  @Override
    public QueryBillsResponse queryBills(QueryBillsRequest remoteRequest) {

        QueryBillsResponse queryBillResponse = new QueryBillsResponse();
        setBaseFields(remoteRequest, queryBillResponse);

        Holder<ArrayOfBorcBilgisi> kentliBorcBilgileriniGetirResult = null;
        Holder<Sonuc> sonuc = null;
        getSeferihisarService().kentliBorcBilgileriniGetir
                (null,new BigDecimal(remoteRequest.getIdentityNo()),null,null,kentliBorcBilgileriniGetirResult,sonuc);

        String responseInternalResultCode = String.valueOf(sonuc.value.getHataKodu());
        resolveResponseMessage(responseInternalResultCode, queryBillResponse);



        if (BillPaymentsConsts.RESPONSE_STATUS.SUCCESS.equals(queryBillResponse.getStatus())) {

            BaseBillDTO billDTO = new BaseBillDTO();

            List<BaseBillDTO> billDTOList = kentliBorcBilgileriniGetirResult.value.getBorcBilgisi().stream().map(billInfo -> {

                billInfo.getBorcDetaylari().getValue().getBorcDetayi().stream().map(borcDetayi -> {
                    billDTO.setBillNo(String.valueOf(borcDetayi.getBeyanSiraNo()));
                    billDTO.setInfo7(String.valueOf(borcDetayi.getYil()));

                }).collect(Collectors.toList());
                billDTO.setProduct(remoteRequest.getProduct());
                billDTO.setInstitution(remoteRequest.getInstitution());
                billDTO.setSubscriberNo(remoteRequest.getSubscriberNumber1());
                billDTO.setBillIssueDate(LocalDate.now());
                billDTO.setBillDueDate(billDTO.getBillDueDate());
                billDTO.setBillLoadDate(LocalDate.now());
                billDTO.setSubscriberName(String.valueOf(billInfo.getAdSoyad()));
                billDTO.setSubscriberNo(remoteRequest.getSubscriberNumber1());
                billDTO.setInstitutionServiceType(remoteRequest.getInstitutionServiceType());
                billDTO.setCurrency(remoteRequest.getCurrencyCode());
                billDTO.setBillAmount(new BigDecimal(String.valueOf(billInfo.getToplamTutar())));
                return billDTO;
            }).toList();

            queryBillResponse.setBills(billDTOList);
        }
