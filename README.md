if( wsResponse != null ) {
					// setting institution result info
					responseInstitutionResultCode = wsResponse.getKod();
					responseInstitutionResultDetail = wsResponse.getAciklama();
					resolveResponseMessage( responseInstitutionResultCode, remoteResponse );
					
					// For 'SUCCESS' state of service result.
					if( EnumResponseCodes.SUCCESS.getValue().equals( remoteResponse.getInternalResultCode() ) ) {
						if( wsResponse.getKayit() != null && wsResponse.getKayit().getAboneBorcs() != null && wsResponse.getKayit().getAboneBorcs().size() > 0 ) {
							
							int numberOfRecords = wsResponse.getKayit().getAboneBorcs().size();
							BigDecimal calculatedBillAmount = null;
							// convert bill map to bill list
							List<BaseBillDTO> billList = new ArrayList<BaseBillDTO>();
							for( int i = 0; i < numberOfRecords; i++ ) {
								BaseBillDTO billDTO = null;
								AboneBorc aboneBorc = wsResponse.getKayit().getAboneBorcs().get( i );
								// Check bill due date for incoming bills.
								long todayTime = PYMDateUtils.getTruncatedTime().getTime();
								if( aboneBorc != null 
										&& aboneBorc.getBelgeNo() != null // Checking bill no is exist.
										&& aboneBorc.getSonOdemeTarih() != null && aboneBorc.getSonOdemeTarih().toGregorianCalendar().getTimeInMillis() >= todayTime ) {
									billDTO = new BaseBillDTO();
									billDTO.setProduct( i_remoteRequestDTO.getProduct() );
									billDTO.setInstitution( i_remoteRequestDTO.getInstitution() );
									billDTO.setBillNo( aboneBorc.getBelgeNo() );
									billDTO.setSubscriberNo( aboneBorc.getAboneNo() != null ? aboneBorc.getAboneNo().toString() : null );
									billDTO.setBillDueDate( aboneBorc.getSonOdemeTarih() != null ? aboneBorc.getSonOdemeTarih().toGregorianCalendar().getTime() : null );
									billDTO.setInstitutionServiceType( i_remoteRequestDTO.getInstitutionServiceType() );
									billDTO.setBankReferenceNo( null ); // will be assigned in the process
									// Subscriber name setting
									StringBuilder sb = new StringBuilder();
									sb.append( aboneBorc.getAd() ).append( " " ).append( aboneBorc.getSoyad() );
									String subscriberName = sb.toString();
									billDTO.setSubscriberName( subscriberName.length() > 50 ? subscriberName.substring( 0, 50 ) : subscriberName );
									billDTO.setSubscriberSurname( null );
									billDTO.setBillTerm( null );
									billDTO.setBillIssueDate( null );
									// calculate the bill amount
									calculatedBillAmount = new BigDecimal( "0.0" );
									calculatedBillAmount = calculatedBillAmount.add( aboneBorc.getTutar() != null ? aboneBorc.getTutar() : new BigDecimal( "0.0" ) );
									billDTO.setBillAmount( calculatedBillAmount );
									billDTO.setBillRecalculatedAmount( billDTO.getBillAmount() );
									billDTO.setStatus( EnumBillStatus.NOT_PAID.getValue() );
									billDTO.setCurrency( EnumCurrencyCodes.TURKISH_LIRA.getValue() );
									billDTO.setInfo1( null );
									if( aboneBorc.getAnahtar() != null ) {
										if( aboneBorc.getAnahtar().length() <= 100 ) {
											billDTO.setInfo2( aboneBorc.getAnahtar().length() > 50 ? aboneBorc.getAnahtar().substring( 0, 50 ) : aboneBorc.getAnahtar() );
											billDTO.setInfo3( aboneBorc.getAnahtar().length() > 50 ? aboneBorc.getAnahtar().substring( 50, aboneBorc.getAnahtar().length() ) : null );
										}
										else {
											billDTO.setInfo2( aboneBorc.getAnahtar().length() > 50 ? aboneBorc.getAnahtar().substring( 0, 50 ) : aboneBorc.getAnahtar() );
											billDTO.setInfo3( aboneBorc.getAnahtar().length() > 50 ? aboneBorc.getAnahtar().substring( 50, 100 ) : null );
										}
									}
									billDTO.setInfo4( aboneBorc.getBelgeNo() );
									billDTO.setInfo5( null );
									billDTO.setInfo6( null );
									billDTO.setInfo7( null );
									billDTO.setInfo8( null );
									billDTO.setInfo9( null );
									billDTO.setFactoringInstitutionCode( null );
									// put bill to bill map
									billList.add( billDTO );
								}
							} 
							
							// set bill list to the response
							remoteResponse.setBills( billList );
						}
						else {
							// No bills found.
							responseStatus = BillPaymentsConsts.RESPONSE_STATUS.SUCCESS;
			        		responseInternalResultCode = EnumResponseCodes.BILL__NO_BILLS_FOUND.getValue();
			        		responseInternalResultDetail = EnumResponseCodes.BILL__NO_BILLS_FOUND.getDescription();
						}
					}
