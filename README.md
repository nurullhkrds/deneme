@Override
public QueryBillsResponse queryBills(QueryBillsRequest remoteRequest) {
    QueryBillsResponse queryBillResponse = new QueryBillsResponse();
    setBaseFields(remoteRequest, queryBillResponse);

    if (!remoteRequest.getSubscriberNumber1().isEmpty()) {
        ResultOfListOfAboneBorc wsResponse = getSuKilisService().borcSor(INSTITUTION_CODE, remoteRequest.getSubscriberNumber1(), OPERATION_TYPE);

        if (wsResponse != null) {
            String responseInstitutionResultCode = wsResponse.getKod().getValue();
            resolveResponseMessage(responseInstitutionResultCode, queryBillResponse);

            if (EnumResponseCodes.SUCCESS.getValue().equals(queryBillResponse.getInternalResultCode())) {
                List<AboneBorc> borcList = wsResponse.getKayit().getValue().getAboneBorc();
                if (borcList != null && !borcList.isEmpty()) {
                    List<BaseBillDTO> billList = new ArrayList<>();
                    for (AboneBorc aboneBorc : borcList) {
                        if (aboneBorc != null && aboneBorc.getBelgeNo() != null) {
                            BaseBillDTO billDTO = new BaseBillDTO();
                            billDTO.setProduct(remoteRequest.getProduct());
                            billDTO.setInstitution(remoteRequest.getInstitution());
                            billDTO.setBillNo(aboneBorc.getBelgeNo().getValue());
                            billDTO.setSubscriberNo(remoteRequest.getSubscriberNumber1());
                            billDTO.setSubscriberNoPart1(remoteRequest.getSubscriberNumber1());
                            billDTO.setSubscriberNoPart2(remoteRequest.getSubscriberNumber2());
                            billDTO.setSubscriberNoPart3(remoteRequest.getSubscriberNumber3());
                            billDTO.setBillDueDate(aboneBorc.getSonOdemeTarih() != null ? aboneBorc.getSonOdemeTarih().toGregorianCalendar().getTime() : null);
                            billDTO.setInstitutionServiceType(remoteRequest.getInstitutionServiceType());
                            StringBuilder sb = new StringBuilder();
                            sb.append(aboneBorc.getAd()).append(" ").append(aboneBorc.getSoyad());
                            String subscriberName = sb.toString();
                            billDTO.setSubscriberName(subscriberName.length() > 50 ? subscriberName.substring(0, 50) : subscriberName);
                            BigDecimal calculatedBillAmount = new BigDecimal("0.0").add(aboneBorc.getTutar() != null ? aboneBorc.getTutar() : new BigDecimal("0.0"));
                            billDTO.setBillAmount(calculatedBillAmount);
                            billDTO.setBillRecalculatedAmount(calculatedBillAmount);
                            billDTO.setStatus(EnumBillStatus.NOT_PAID.getValue());
                            billDTO.setCurrency(remoteRequest.getCurrencyCode());
                            setInfoFields(aboneBorc.getAnahtar(), billDTO);
                            billDTO.setInfo4(aboneBorc.getBelgeNo().getValue());
                            billList.add(billDTO);
                        }
                    }
                    queryBillResponse.setBills(billList);
                } else {
                    queryBillResponse.setStatus(BillPaymentsConsts.RESPONSE_STATUS.SUCCESS);
                    queryBillResponse.setInstitutionResultCode(EnumResponseCodes.BILL__NO_BILLS_FOUND.getValue());
                    queryBillResponse.setInstitutionResultDetail(EnumResponseCodes.BILL__NO_BILLS_FOUND.getDescription());
                }
            }

            setRemoteResponseData(queryBillResponse, remoteRequest, PYMLogUtil.convertObjectToJsonString("mutabakatDetay"), PYMLogUtil.convertObjectToJsonString(wsResponse));
        } else {
            queryBillResponse.setStatus(BillPaymentsConsts.RESPONSE_STATUS.ERROR);
            queryBillResponse.setInternalResultCode(EnumResponseCodes.INSTITUTION__UNKNOWN_ERROR.getValue());
            queryBillResponse.setInternalResultDetail(ProcessConsts.INTERNAL_ERROR_CODES.IA__NULL_REQUEST_RECEIVED);
        }
    } else {
        queryBillResponse.setStatus(BillPaymentsConsts.RESPONSE_STATUS.ERROR);
        queryBillResponse.setInternalResultCode(EnumResponseCodes.INSTITUTION__UNKNOWN_ERROR.getValue());
        queryBillResponse.setInternalResultDetail(ProcessConsts.INTERNAL_ERROR_CODES.IA__NULL_REQUEST_RECEIVED);
    }
    return queryBillResponse;
}

private void setInfoFields(JAXBElement<String> anahtar, BaseBillDTO billDTO) {
    if (anahtar != null) {
        String key = anahtar.getValue();
        if (key.length() > 50) {
            billDTO.setInfo2(key.substring(0, 50));
            billDTO.setInfo3(key.length() > 100 ? key.substring(50, 100) : key.substring(50));
        } else {
            billDTO.setInfo2(key);
            billDTO.setInfo3(null);
        }
    }
}
