 @Override
    public QueryBillsResponse queryBills(QueryBillsRequest remoteRequest) {

        QueryBillsResponse queryBillResponse = new QueryBillsResponse();
        setBaseFields(remoteRequest, queryBillResponse);

        if(!remoteRequest.getSubscriberNumber1().isEmpty()){

            ResultOfListOfAboneBorc wsResponse=getSuKilisService().borcSor(INSTITUTION_CODE,remoteRequest.getSubscriberNumber1(),OPERATION_TYPE);


            if( wsResponse != null ) {

                String responseInstitutionResultCode = wsResponse.getKod().getValue();

                resolveResponseMessage(responseInstitutionResultCode, queryBillResponse);

                if( EnumResponseCodes.SUCCESS.getValue().equals( queryBillResponse.getInternalResultCode() ) ) {
                    if( wsResponse.getKayit() != null && wsResponse.getKayit().getValue().getAboneBorc() != null && !wsResponse.getKayit().getValue().getAboneBorc().isEmpty()) {

                        int numberOfRecords = wsResponse.getKayit().getValue().getAboneBorc().size();
                        BigDecimal calculatedBillAmount ;
                        // convert bill map to bill list
                        List<BaseBillDTO> billList = new ArrayList<BaseBillDTO>();
                        for( int i = 0; i < numberOfRecords; i++ ) {
                            BaseBillDTO billDTO = null;
                            AboneBorc aboneBorc = wsResponse.getKayit().getValue().getAboneBorc().get( i );
                            if( aboneBorc != null && aboneBorc.getBelgeNo() != null ) {
                                billDTO = new BaseBillDTO();
                                billDTO.setProduct( remoteRequest.getProduct() );
                                billDTO.setInstitution( remoteRequest.getInstitution() );
                                billDTO.setBillNo( aboneBorc.getBelgeNo().getValue());
                                billDTO.setSubscriberNo( remoteRequest.getSubscriberNumber1() );
                                billDTO.setSubscriberNoPart1( remoteRequest.getSubscriberNumber1() );
                                billDTO.setSubscriberNoPart2( remoteRequest.getSubscriberNumber2() );
                                billDTO.setSubscriberNoPart3( remoteRequest.getSubscriberNumber3() );
                                billDTO.setBillDueDate( aboneBorc.getSonOdemeTarih() != null ? aboneBorc.getSonOdemeTarih().toGregorianCalendar().getTime() : null );
                                billDTO.setInstitutionServiceType( remoteRequest.getInstitutionServiceType() );
                                // Subscriber name setting
                                StringBuilder sb = new StringBuilder();
                                sb.append( aboneBorc.getAd() ).append( " " ).append( aboneBorc.getSoyad() );
                                String subscriberName = sb.toString();
                                billDTO.setSubscriberName( subscriberName.length() > 50 ? subscriberName.substring( 0, 50 ) : subscriberName );
                                // calculate the bill amount
                                calculatedBillAmount = new BigDecimal( "0.0" );
                                calculatedBillAmount = calculatedBillAmount.add( aboneBorc.getTutar() != null ? aboneBorc.getTutar() : new BigDecimal( "0.0" ) );
                                billDTO.setBillAmount( calculatedBillAmount );
                                billDTO.setBillRecalculatedAmount( billDTO.getBillAmount() );
                                billDTO.setStatus( EnumBillStatus.NOT_PAID.getValue() );
                                billDTO.setCurrency( remoteRequest.getCurrencyCode() );
                                if( aboneBorc.getAnahtar() != null ) {
                                    if( aboneBorc.getAnahtar().getValue().length() <= 100 ) {
                                        billDTO.setInfo2( aboneBorc.getAnahtar().getValue().length() > 50 ? aboneBorc.getAnahtar().getValue().substring( 0, 50 ) : aboneBorc.getAnahtar().getValue() );
                                        billDTO.setInfo3( aboneBorc.getAnahtar().getValue().length() > 50 ? aboneBorc.getAnahtar().getValue().substring( 50) : null );
                                    }
                                    else {
                                        billDTO.setInfo2( aboneBorc.getAnahtar().getValue().length() > 50 ? aboneBorc.getAnahtar().getValue().substring( 0, 50 ) : aboneBorc.getAnahtar().getValue() );
                                        billDTO.setInfo3( aboneBorc.getAnahtar().getValue().length() > 50 ? aboneBorc.getAnahtar().getValue().substring( 50, 100 ) : null );
                                    }
                                }
                                billDTO.setInfo4( aboneBorc.getBelgeNo().getValue() );

                                // put bill to bill map
                                billList.add( billDTO );
                            }
                        }

                        // set bill list to the response
                        queryBillResponse.setBills(billList);
                    }
                    else {
                        // No bills found.
                        queryBillResponse.setStatus(BillPaymentsConsts.RESPONSE_STATUS.SUCCESS);
                        queryBillResponse.setInstitutionResultCode(EnumResponseCodes.BILL__NO_BILLS_FOUND.getValue());
                        queryBillResponse.setInstitutionResultDetail(EnumResponseCodes.BILL__NO_BILLS_FOUND.getDescription());
                    }
                }


                setRemoteResponseData(queryBillResponse, remoteRequest, PYMLogUtil.convertObjectToJsonString("mutabakatDetay"), PYMLogUtil.convertObjectToJsonString(wsResponse));

            }
            else {
                queryBillResponse.setStatus( BillPaymentsConsts.RESPONSE_STATUS.ERROR );
                queryBillResponse.setInternalResultCode( EnumResponseCodes.INSTITUTION__UNKNOWN_ERROR.getValue() );
                queryBillResponse.setInternalResultDetail( ProcessConsts.INTERNAL_ERROR_CODES.IA__NULL_REQUEST_RECEIVED );
            }


        }

        else {
            queryBillResponse.setStatus( BillPaymentsConsts.RESPONSE_STATUS.ERROR );
            queryBillResponse.setInternalResultCode( EnumResponseCodes.INSTITUTION__UNKNOWN_ERROR.getValue() );
            queryBillResponse.setInternalResultDetail( ProcessConsts.INTERNAL_ERROR_CODES.IA__NULL_REQUEST_RECEIVED );
        }
        return queryBillResponse;
    }
