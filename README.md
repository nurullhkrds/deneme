  public FetchPaymentOrderedBillsResponse fetchPaymentOrderedBills(FetchPaymentOrderedBillsRequest remoteRequest) {
Refactor this method to reduce its Cognitive Complexity from 31 to the 15 allowed.
        FetchPaymentOrderedBillsResponse response = new FetchPaymentOrderedBillsResponse();
        setBaseFields(remoteRequest, response);
        ResultOfListOfAboneBorc wsResponse;
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("dd.MM.yyyy");
        String formattedDate = remoteRequest.getDueDate().format(formatter);
        ObjectFactory factory =new ObjectFactory();
        SonOdemeTarihiGelmisBorclar wsRequest = new SonOdemeTarihiGelmisBorclar();
        wsRequest.setClientId(factory.createSonOdemeTarihiGelmisBorclarClientId((getInstitutionBankCode())));
        wsRequest.setTarih(factory.createSonOdemeTarihiGelmisBorclarTarih(formattedDate));
        wsRequest.setTip(factory.createSonOdemeTarihiGelmisBorclarTip(OPERATION_TYPE));
        wsResponse = getArmadasService().sonOdemeTarihiGelmisBorclar( getInstitutionBankCode(),
                formattedDate,
                OPERATION_TYPE );
        if (wsResponse != null) {
            String responseInstitutionResultCode = wsResponse.getKod().getValue();
            resolveResponseMessage(responseInstitutionResultCode, response);
            if (EnumResponseCodes.SUCCESS.getValue().equals(response.getInternalResultCode())) {
                List<AboneBorc> borcList = wsResponse.getKayit().getValue().getAboneBorc();
                if (borcList != null && !borcList.isEmpty()) {
                    List<BaseBillDTO> billList = new ArrayList<>();
                    for (AboneBorc aboneBorc : borcList) {
                        if (aboneBorc != null && aboneBorc.getBelgeNo() != null && isDueDateValid(aboneBorc.getSonOdemeTarih())) {
                            BaseBillDTO billDTO = new BaseBillDTO();
                            billDTO.setProduct(remoteRequest.getProduct());
                            billDTO.setInstitution(remoteRequest.getInstitution());
                            billDTO.setBillNo(aboneBorc.getBelgeNo().getValue());
                            billDTO.setSubscriberNo(
                                    aboneBorc.getAboneNo() != null
                                            ? lpadWithZeros(aboneBorc.getAboneNo().toString(), 10)
                                            : null
                            );
                            billDTO.setBillDueDate( convertToLocalDate(aboneBorc.getSonOdemeTarih()));
                            billDTO.setInstitutionServiceType(remoteRequest.getInstitutionServiceType());
                            String subscriberName = aboneBorc.getAd().getValue() + " " + aboneBorc.getSoyad().getValue();
                            billDTO.setSubscriberName(getAdi(subscriberName));
                            BigDecimal calculatedBillAmount = new BigDecimal("0.0").add(aboneBorc.getTutar() != null ? aboneBorc.getTutar() : new BigDecimal("0.0"));
                            billDTO.setBillAmount(calculatedBillAmount);
                            billDTO.setBillRecalculatedAmount(calculatedBillAmount);
                            billDTO.setStatus(EnumBillStatus.NOT_PAID.getValue());
                            billDTO.setCurrency( EnumCurrencyCodes.TURKISH_LIRA.getValue() );
                            setInfoFields(aboneBorc.getAnahtar(), billDTO);
                            billDTO.setInfo4(aboneBorc.getBelgeNo().getValue());
                            billList.add(billDTO);
                        }
                    }
                    response.setBills(billList);
                } else {
                    response.setStatus(BillPaymentsConsts.RESPONSE_STATUS.SUCCESS);
                    response.setInstitutionResultCode(EnumResponseCodes.BILL__NO_BILLS_FOUND.getValue());
                    response.setInstitutionResultDetail(EnumResponseCodes.BILL__NO_BILLS_FOUND.getDescription());
                }
            }
            setRemoteResponseData(response, remoteRequest, PYMLogUtil.convertObjectToJsonString(wsRequest), PYMLogUtil.convertObjectToJsonString(wsResponse));
        } else {
            response.setStatus(BillPaymentsConsts.RESPONSE_STATUS.ERROR);
            response.setInternalResultCode(EnumResponseCodes.INSTITUTION__UNKNOWN_ERROR.getValue());
            response.setInternalResultDetail(ProcessConsts.INTERNAL_ERROR_CODES.IA__NULL_REQUEST_RECEIVED);
        }
        return response;
