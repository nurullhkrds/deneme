@Override
	public ResponsePerformTellerTransactionAccounting performTellerTransactionAccounting(
			RequestPerformTellerTransactionAccounting request) throws HmnServiceException {
		
		ResponsePerformTellerTransactionAccounting resp = new ResponsePerformTellerTransactionAccounting();
        Long contractNo = null;

        if (!request.getIsReverse()) {
            contractNo = ExternalServiceLocator.getAccountingUtilityService().getContractNumber().getContractNumber();
        } else {
            contractNo = Long.parseLong(request.getHmnAuditLog().getContractNo());
        }
        IPymHmnDateTimeService dateTimeService = ExternalServiceLocator.getDateTimeService();
        IPymAccountingService pymAccountingService = ExternalServiceLocator.getAccountingService();      

        Integer asofdate = dateTimeService.dateToRelDay(request.getHmnAuditLog().getActualTrxDate());
        PymMakeAccountingTellerTransactionDTO makeAccountingDTO = new PymMakeAccountingTellerTransactionDTO();

        makeAccountingDTO.setAuditLogDto(request.getHmnAuditLog());
        makeAccountingDTO.setAuditMasterDto(request.getHmnAuditMaster());
        String vKriter = null;
        Map<String, String> tfsLogother = request.getHmnAuditLogOtherMap();
        
        String productCode = tfsLogother.get("URUN");
        
        if ("DBS".equalsIgnoreCase(productCode)){
        	
        	// plsql paketi çağrılacak.
        }

        List<PymAccReqItemsDTO> itemDtoList = new ArrayList<PymAccReqItemsDTO>();
        PymAccReqItemsDTO creditItemDto = new PymAccReqItemsDTO();
        PymAccReqItemsDTO debitItemDto = new PymAccReqItemsDTO();
        
        //Hesap kontrolü process de ki örneğine bakılacak... bloke ve geçerlilik kontrolü
        //müşteri no kontrolü
        String isDummyMerch = request.getHmnAuditLogOtherMap().get("IS_DUMMY_MERCH");
        String accountingTemplateCode = null;
        
        if (request.getHmnAuditLog().getPaymenttype().equals("4")){ //KART ISE
        	
        	if (isDummyMerch != null && isDummyMerch.equalsIgnoreCase("E") ){ // DUMMY ise muhasebe bizim tarafımızdan yapılır.
        		
        		accountingTemplateCode ="ODMSISTFATODEKK";
        		
        		// debit - > paranın çıktığı hesap 
        		
                debitItemDto.setAccountingTemplateCode(accountingTemplateCode);
                debitItemDto.setOperationalBranch(request.getHmnAuditLog().getActualBranchCode());
                debitItemDto.setBranch(request.getHmnAuditLog().getActualBranchCode());
                debitItemDto.setClientNo(request.getHmnAuditLog().getCifno() != null ? request.getHmnAuditLog().getCifno() : 0 );
                debitItemDto.setAccountingContractType("VZOD");
                debitItemDto.setAccountingEventType("TFS");
                debitItemDto.setNarrative(getNarrative(productCode,tfsLogother));
                debitItemDto.setReceiptNarrative(request.getHmnAuditLog().getNarrative()); // 34 e substring yapılacak
                debitItemDto.setContractNo(contractNo);
                debitItemDto.setUserCode(request.getHmnAuditMaster().getUserId()); // ytodo  gsd05.gw_usercode
                debitItemDto.setChannelCode("SUBE");
                debitItemDto.setDepartment(null); // sgk da null çekilmiş daha sonra bakılacak...
                debitItemDto.setAmount(request.getHmnAuditLog().getTlamount());
                debitItemDto.setCurrency(request.getHmnAuditLog().getCurr());
                debitItemDto.setReceiptGenReference1(request.getHmnAuditLog().getHmnRef()); // bimref
                debitItemDto.setReceiptCode(tfsLogother.get("ABNDEKONT")); 
                debitItemDto.setPrintReceipt("N");
                debitItemDto.setAvaibleRelativeDay(asofdate);
                debitItemDto.setAccountingRelativeDay(asofdate);
                
                //credit paranın girdiği hesap
                creditItemDto.setAccountingTemplateCode(accountingTemplateCode);
                creditItemDto.setOperationalBranch(request.getHmnAuditLog().getActualBranchCode());
                creditItemDto.setBranch(request.getHmnAuditLog().getActualBranchCode());
                //creditItemDto.setClientNo(request.getHmnAuditLog().getCifno() != null ? request.getHmnAuditLog().getCifno() : 0 );
                creditItemDto.setAccountingContractType("VZOD");
                creditItemDto.setAccountingEventType("TFS");
                creditItemDto.setNarrative(getNarrative(productCode,tfsLogother));
                creditItemDto.setReceiptNarrative(request.getHmnAuditLog().getNarrative());
                creditItemDto.setContractNo(contractNo);
                creditItemDto.setUserCode(request.getHmnAuditMaster().getUserId()); // ytodo  gsd05.gw_usercode
                creditItemDto.setChannelCode("SUBE");
                creditItemDto.setDepartment(null); // sgk da null çekilmiş daha sonra bakılacak...
                creditItemDto.setAmount(request.getHmnAuditLog().getTlamount());
                creditItemDto.setCurrency(request.getHmnAuditLog().getCurr());
                
                //Aşağıdaki 3 alan pl sqlde credit için setlenmemiş ???
                
                //creditItemDto.setReceiptGenReference1(request.getHmnAuditLog().getHmnRef()); // bimref
                //creditItemDto.setReceiptCode(tfsLogother.get("ABNDEKONT")); 
                //creditItemDto.setPrintReceipt("N");
                
                creditItemDto.setAvaibleRelativeDay(asofdate);
                creditItemDto.setAccountingRelativeDay(asofdate);

        	}
        	
        }else if (request.getHmnAuditLog().getPaymenttype().equals("3")){ // hesap ise
        	

        	try {
        		
        		//müşteri hesabı check ediliyor (debit)
        		checkAccountSubscriber(request.getHmnAuditLog().getTrxCode(),
        				request.getHmnAuditMaster().getBranchCode(),
        				request.getIsReverse(),
        				request.getHmnAuditLog().getCifno(),
        				request.getHmnAuditLog().getAccountno().toString());
        		
        		// Kurum hesabını check ediyor...(credit)
        		String normalizedAccountNo = normalizeAccountNo(request.getHmnAuditLogOtherMap().get("POST_ACCNUM"));
        		
        		checkAccountInstitution(request.getHmnAuditLog().getTrxCode(),
        				request.getHmnAuditMaster().getBranchCode(),
        				request.getIsReverse(),normalizedAccountNo);
        		
        		
   			 	CastmDTO castm = ExternalServices.getAccountInformationWithoutException(normalizedAccountNo);

        		//credit
        		accountingTemplateCode= "ODMSISTFATODEHS";
        		creditItemDto.setAccountingContractType("VZOD");
                creditItemDto.setAccountingEventType("TFS");
                creditItemDto.setCurrency(request.getHmnAuditLog().getCurr());
                creditItemDto.setDepartment(null); // sgk da null çekilmiş daha sonra bakılacak...
                creditItemDto.setUserCode(request.getHmnAuditMaster().getUserId()); // ytodo  gsd05.gw_usercode
                creditItemDto.setContractNo(contractNo);
                creditItemDto.setOperationalBranch(request.getHmnAuditLog().getActualBranchCode());
                creditItemDto.setReceiptNarrative(request.getHmnAuditLog().getNarrative());
                creditItemDto.setNarrative(getNarrative(productCode,tfsLogother));
                creditItemDto.setAccountingTemplateCode(accountingTemplateCode);
                creditItemDto.setAccountNumber(normalizedAccountNo);
                creditItemDto.setClientNo(castm.getCastmClientNo());
                creditItemDto.setAmount(request.getHmnAuditLog().getAmount());
                creditItemDto.setAvaibleRelativeDay(asofdate);
                creditItemDto.setAccountingRelativeDay(asofdate);
                
                creditItemDto.setReceiptCode("ODMFATURAB"); // Normalde ctype SITL ise abndekont u basmış değilse krmdekont u basmış
                											// fakat krmdekont hep boş logother tablosunda...
                
                creditItemDto.setStatisticalCode(request.getHmnAuditLog().getStatcode() !=null ? Integer.parseInt( request.getHmnAuditLog().getStatcode()) : 0);
                creditItemDto.setFxposRule(" ");
                creditItemDto.setAccountingOperationType(request.getIsReverse() ? "R" : "P");
                creditItemDto.setChannelCode("ŞUBE");
                creditItemDto.setReceiptGenReference1(request.getHmnAuditLog().getHmnRef()); // bimref
                creditItemDto.setReceiptNarrative5(request.getHmnAuditLog().getExplanation());
                //creditItemDto.setMatchingAmountNo(matchingAmountNo); SITL ve currency kontrolü var // SITL diye bir kontrol yapılacak mı ?
                creditItemDto.setExchangeRate(request.getHmnAuditLog().getRate());

                
                
                
                
                debitItemDto.setAccountingTemplateCode(accountingTemplateCode);
                debitItemDto.setAccountNumber(normalizeAccountNo(request.getHmnAuditLog().getAccountno().toString()));
                if("YTL".equals(request.getHmnAuditLog().getCurr())){
                	debitItemDto.setAmount(request.getHmnAuditLog().getTlamount());
                	debitItemDto.setMatchingAmountNo(9);

                }else{
                	debitItemDto.setAmount(request.getHmnAuditLog().getAmount());
                	debitItemDto.setMatchingAmountNo(19);


                }
                debitItemDto.setCurrency(request.getHmnAuditLog().getCurr());
                debitItemDto.setClientNo(request.getHmnAuditLog().getCifno() != null ? request.getHmnAuditLog().getCifno() : 0 );
                debitItemDto.setContractNo(contractNo);
                debitItemDto.setUserCode(request.getHmnAuditMaster().getUserId()); // ytodo  gsd05.gw_usercode
                debitItemDto.setAccountingContractType("VZOD");
                debitItemDto.setAccountingEventType("TFS");
                debitItemDto.setOperationalBranch(request.getHmnAuditLog().getActualBranchCode());
                debitItemDto.setBranch(request.getHmnAuditLog().getActualBranchCode());
                debitItemDto.setReceiptNarrative(request.getHmnAuditLog().getNarrative()); // 34 e substring yapılacak
                debitItemDto.setNarrative(getNarrative(productCode,tfsLogother));
                debitItemDto.setAvaibleRelativeDay(asofdate);
                debitItemDto.setAccountingRelativeDay(asofdate);
                debitItemDto.setStatisticalCode(request.getHmnAuditLog().getStatcode() !=null ? Integer.parseInt( request.getHmnAuditLog().getStatcode()) : 0);
                debitItemDto.setDepartment(null); // sgk da null çekilmiş daha sonra bakılacak...
                debitItemDto.setFxposRule(" ");
                debitItemDto.setUserCode(request.getHmnAuditMaster().getUserId()); // ytodo  gsd05.gw_usercode
                debitItemDto.setAccountingOperationType(request.getIsReverse() ? "R" : "P");
                debitItemDto.setChannelCode("ŞUBE");
                debitItemDto.setReceiptCode("ODMFATURAB"); // debitte direk abndekont u basmış bu da buna denk geliyor...
                debitItemDto.setExchangeRate(new BigDecimal(tfsLogother.get("PAYRATE")));
                debitItemDto.setReceiptGenReference1(request.getHmnAuditLog().getHmnRef()); // bimref
                debitItemDto.setReceiptNarrative5(request.getHmnAuditLog().getExplanation());

         
        	}
        	catch (Exception e){
        		
        	}

        }
        else if (request.getHmnAuditLog().getPaymenttype().equals("1")){ // Cash ise
        	
        	try {
        		
         		//müşteri hesabı check ediliyor (debit)
        		checkAccountSubscriber(request.getHmnAuditLog().getTrxCode(),
        				request.getHmnAuditMaster().getBranchCode(),
        				request.getIsReverse(),
        				request.getHmnAuditLog().getCifno(),
        				request.getHmnAuditLog().getAccountno().toString());
        		
        		// Kurum hesabını check ediyor...(credit)
        		String normalizedAccountNo = normalizeAccountNo(request.getHmnAuditLogOtherMap().get("POST_ACCNUM"));
        		
        		checkAccountInstitution(request.getHmnAuditLog().getTrxCode(),
        				request.getHmnAuditMaster().getBranchCode(),
        				request.getIsReverse(),normalizedAccountNo);
        		
        		
    			CastmDTO castm = ExternalServices.getAccountInformationWithoutException(normalizedAccountNo);

        		//credit
    			creditItemDto.setAccountingContractType("VZOD");
                creditItemDto.setAccountingEventType("TFS");
                creditItemDto.setCurrency(request.getHmnAuditLog().getCurr());
                creditItemDto.setDepartment(null); // sgk da null çekilmiş daha sonra bakılacak...
                creditItemDto.setUserCode(request.getHmnAuditMaster().getUserId()); // ytodo  gsd05.gw_usercode
                creditItemDto.setContractNo(contractNo);
                creditItemDto.setOperationalBranch(request.getHmnAuditLog().getActualBranchCode());
                creditItemDto.setReceiptNarrative(request.getHmnAuditLog().getNarrative());
                creditItemDto.setNarrative(getNarrative(productCode,tfsLogother));
                
                if ("YTL".equals(request.getHmnAuditLog().getCurr().trim())){
                	accountingTemplateCode ="ODMSISTFATRNKTL";
                	creditItemDto.setMatchingAmountNo(6);

                }else {
                	accountingTemplateCode ="ODMSISTFATRNKYP";
                	creditItemDto.setMatchingAmountNo(16);


                }
                creditItemDto.setAccountingTemplateCode(accountingTemplateCode);
                creditItemDto.setAccountNumber(normalizedAccountNo);
                creditItemDto.setAmount(request.getHmnAuditLog().getAmount());
                creditItemDto.setAvaibleRelativeDay(asofdate);
                creditItemDto.setAccountingRelativeDay(asofdate);
                creditItemDto.setReceiptCode("ODMFATURAB"); // Normalde ctype SITL ise abndekont u basmış değilse krmdekont u basmış
															// fakat krmdekont hep boş logother tablosunda...
                
                creditItemDto.setStatisticalCode(request.getHmnAuditLog().getStatcode() !=null ? Integer.parseInt( request.getHmnAuditLog().getStatcode()) : 0);
                creditItemDto.setFxposRule(" ");
                creditItemDto.setAccountingOperationType(request.getIsReverse() ? "R" : "P");
                creditItemDto.setChannelCode("ŞUBE");
                creditItemDto.setReceiptGenReference1(request.getHmnAuditLog().getHmnRef()); // bimref
                creditItemDto.setReceiptNarrative5(request.getHmnAuditLog().getExplanation());
                
                
                
                
                
                
                debitItemDto.setAccountNumber(normalizeAccountNo(request.getHmnAuditLog().getAccountno().toString()));
                
                if("YTL".equals(request.getHmnAuditLog().getCurr())){
                	debitItemDto.setAmount(request.getHmnAuditLog().getTlamount());
                	accountingTemplateCode = "ODMSISTFATRNKTL";
                	debitItemDto.setMatchingAmountNo(8);

                }else{
                	debitItemDto.setAmount(request.getHmnAuditLog().getAmount());
                	accountingTemplateCode = "ODMSISTFATRNKYP";
                	debitItemDto.setMatchingAmountNo(18);

                }
                debitItemDto.setAccountingTemplateCode(accountingTemplateCode);
                debitItemDto.setCurrency(request.getHmnAuditLog().getCurr());
                debitItemDto.setClientNo(request.getHmnAuditLog().getCifno() != null ? request.getHmnAuditLog().getCifno() : 0 );
                debitItemDto.setContractNo(contractNo);
                debitItemDto.setUserCode(request.getHmnAuditMaster().getUserId()); // ytodo  gsd05.gw_usercode
                debitItemDto.setAccountingContractType("VZOD");
                debitItemDto.setAccountingEventType("TFS");
                debitItemDto.setOperationalBranch(request.getHmnAuditLog().getActualBranchCode());
                debitItemDto.setBranch(request.getHmnAuditLog().getActualBranchCode());
                debitItemDto.setReceiptNarrative(request.getHmnAuditLog().getNarrative()); // 34 e substring yapılacak
                debitItemDto.setNarrative(getNarrative(productCode,tfsLogother));
                debitItemDto.setAvaibleRelativeDay(asofdate);
                debitItemDto.setAccountingRelativeDay(asofdate);
                debitItemDto.setStatisticalCode(request.getHmnAuditLog().getStatcode() !=null ? Integer.parseInt( request.getHmnAuditLog().getStatcode()) : 0);
                debitItemDto.setDepartment(null); // sgk da null çekilmiş daha sonra bakılacak...
                debitItemDto.setFxposRule(" ");
                debitItemDto.setUserCode(request.getHmnAuditMaster().getUserId()); // ytodo  gsd05.gw_usercode
                debitItemDto.setAccountingOperationType(request.getIsReverse() ? "R" : "P");
                debitItemDto.setChannelCode("ŞUBE");
                debitItemDto.setReceiptCode("ODMFATURAB"); // debitte direk abndekont u basmış bu da buna denk geliyor...
                debitItemDto.setExchangeRate(new BigDecimal(tfsLogother.get("PAYRATE")));
                debitItemDto.setReceiptGenReference1(request.getHmnAuditLog().getHmnRef()); // bimref
                debitItemDto.setReceiptNarrative5(request.getHmnAuditLog().getExplanation());
        	}
        	catch (Exception e){
        		
        	}
    
        }
        
        
        itemDtoList.add(debitItemDto);
        itemDtoList.add(creditItemDto);

        makeAccountingDTO.setAccReqItemsList(itemDtoList);
        
        PymMakeAccountingResultDTO resultDTO = pymAccountingService.makeAccountingTellerTransaction(makeAccountingDTO);

        
        if (resultDTO == null || resultDTO.getErrorCode() != 0) {

            // ytofo log lanacak
        	BillPaymentUtils.throwAccountingErrorMsg(" **ERRORMESSAGE:" + resultDTO.getErrorMessage() + "***SQLERRORCODE:" + resultDTO.getErrorCode() + "**SQLERRTEXT:"
                    + resultDTO.getSqlMessage() + "**vItemNo:" + resultDTO.getErrorItemNo());
        } else {
            resp.setContractNo(resultDTO.getAccResReturnLinesList().get(0).getContractNo().toString());
        }

        BigDecimal receiptAmount = request.getHmnAuditLog().getTlamount();
        if (!request.getIsReverse()){
            receiptAmount = receiptAmount.negate();
        }
      
        if (request.getHmnAuditLog().getPaymenttype().equals("4")) { //CARD
            if (request.getIsReverse()){                
                boolean isSuccess = BillPaymentUtils.reversePrintReceipt(contractNo, request.getHmnAuditMaster().getUserId());
                if (!isSuccess) {
                	BillPaymentUtils.throwAccountingErrorMsg(" iptal dkntmaster kaydi olusturulamadi!");
                }
            }else{
                PymAddPrintReceiptResultDTO printReceiptResultDTO = BillPaymentUtils.printReceipt(request.getHmnAuditLog().getHmnRef(), request.getHmnAuditLog().getCifno(),
                        PYMStringUtils.getSubtring(request.getHmnAuditLog().getNarrative()+" / "+" "+" / " +request.getHmnAuditLog().getExplanation(), 350), receiptAmount, 
                        contractNo, request.getHmnAuditLog().getCreditcardno(), "SUBE", "SGKT", "KKP", request.getHmnAuditMaster().getUserId());

                if (!printReceiptResultDTO.isSuccess()) {
                	BillPaymentUtils.throwAccountingErrorMsg(" dkntmaster kaydi olusturulamadi!");
                }
            }
        }
        
        
        
        if (request.getHmnAuditLog().getPaymenttype().equals("1")) { // Nakit dekont
            if (request.getIsReverse()) {
                boolean isSuccess = BillPaymentUtils.reversePrintReceipt(contractNo, request.getHmnAuditMaster().getUserId());
                if (!isSuccess) {
                	BillPaymentUtils.throwAccountingErrorMsg(" iptal dkntmaster kaydi olusturulamadi!");
                }
            } else {
                PymAddPrintReceiptResultDTO printReceiptResultDTO = BillPaymentUtils.printReceipt(request.getHmnAuditLog().getHmnRef(),
                        request.getHmnAuditLog().getCifno(), PYMStringUtils.getSubtring(request.getHmnAuditLog().getNarrative()+" / "
                                + request.getHmnAuditLog().getExplanation(), 350),receiptAmount, contractNo, request.getHmnAuditLog()
                                .getCreditcardno(), "SUBE", "SGKT", "TFS", request.getHmnAuditMaster().getUserId());

                if (!printReceiptResultDTO.isSuccess()) {
                	BillPaymentUtils.throwAccountingErrorMsg(" dkntmaster kaydi olusturulamadi!");
                }
            }
        }
        resp.setContractNo(contractNo.toString());
		return resp;
	}
	
	
	
	

	
	
	@SuppressWarnings("unused")
	private String getNarrative (String product,Map<String, String> map) {
		
		if (product == "TAHSİLAT") {
			return buildTahsilatNarrative (map);
		}
		
		return "tfs_log.narrative";
		
		
	}
	
	private String buildTahsilatNarrative (Map<String, String> map){
		
		return safeGet ("NARRATIVE1",map)
				+ safeGet ("NARRATIVE2",map)
				+ safeGet ("NARRATIVE3",map)
				+ safeGet ("NARRATIVE4",map);
	}
	
	
	private String safeGet (String narrative,Map<String, String> map){
		try {
			String value = map.get(narrative);
			return value;
		}
		catch (Exception e){
			return "";
		}
	}
	
	private void checkAccountSubscriber(String trxCode,String branchCode,Boolean reverseFlag,Integer cifNo,String tfsLogAccStr){
		
		String vKriter;
		try {
			
			 CastmDTO castm = ExternalServices.getAccountInformationWithoutException(tfsLogAccStr);
	         
	         if (castm == null) {
	             BillPaymentUtils.throwAccountingErrorMsg("Hesap Bulunamadı!");

	          } else if (castm.getCastmStatusCde() != 1) {
	              BillPaymentUtils.throwAccountingErrorMsg("Hesap Geçersiz!");

	          }	else if ("D".equals(castm.getCastmMaint().trim())){
	              BillPaymentUtils.throwAccountingErrorMsg("Hesap Geçersiz!");

	          }

	        boolean isHsriad = "HSRIAD".equals(trxCode);

	        if (reverseFlag) {
	         vKriter = isHsriad ? "C" : "D";
	        } else {
	         vKriter = isHsriad ? "D" : "C";
	        }
	         
	         if("B".equals(castm.getCastmBlockCode()) || vKriter.equals(castm.getCastmBlockCode())){
	             BillPaymentUtils.throwAccountingErrorMsg("Hesapta bloke var!");

	         }
	         
	         if(("B".equals(castm.getCastmBrnBlock()) || vKriter.equals(castm.getCastmBrnBlock())) && !branchCode.equals(castm.getCastmBranch())){
	             BillPaymentUtils.throwAccountingErrorMsg("Hesapta şube blokesi var!");

	         }
	         
	         if(("B".equals(castm.getCastmGmBloke()) || vKriter.equals(castm.getCastmGmBloke())) && !"925".equals(branchCode)){
	             BillPaymentUtils.throwAccountingErrorMsg("Hesapta GM blokesi var!");

	         }
			
		}catch(Exception e){
			
		}
		
	}
	
	
	private void checkAccountInstitution(String trxCode,String branchCode,Boolean reverseFlag,String accountNo){
		
		String vKriter;
		try {
			
			 CastmDTO castm = ExternalServices.getAccountInformationWithoutException(accountNo);
	         
	         if (castm == null) {
	             BillPaymentUtils.throwAccountingErrorMsg("Hesap Bulunamadı!");

	          } else if (castm.getCastmStatusCde() != 1) {
	              BillPaymentUtils.throwAccountingErrorMsg("Kurum Hesabı Geçersiz!");

	          }	else if ("D".equals(castm.getCastmMaint().trim())){
	              BillPaymentUtils.throwAccountingErrorMsg("Kurum Hesabı Geçersiz!");

	          }
	        

	        boolean isHsriad = "HSRIAD".equals(trxCode);

	        if (reverseFlag) {
	         vKriter = isHsriad ? "C" : "D";
	        } else {
	         vKriter = isHsriad ? "D" : "C";
	        }
	         
	         if("B".equals(castm.getCastmBlockCode()) || vKriter.equals(castm.getCastmBlockCode())){
	             BillPaymentUtils.throwAccountingErrorMsg("Kurum hesabında bloke var!");

	         }
	         
	         if(("B".equals(castm.getCastmBrnBlock()) || vKriter.equals(castm.getCastmBrnBlock())) && !branchCode.equals(castm.getCastmBranch())){
	             BillPaymentUtils.throwAccountingErrorMsg("Kurum hesabında şube blokesi var!");

	         }
	         
	         if(("B".equals(castm.getCastmGmBloke()) || vKriter.equals(castm.getCastmGmBloke())) && !"925".equals(branchCode)){
	             BillPaymentUtils.throwAccountingErrorMsg("Kurum hesabında GM blokesi var!");

	         }
			
		}catch(Exception e){
			
		}
	}
	
	
	private String normalizeAccountNo(String accountNo) {
	    if (accountNo == null) {
	        return null;
	    }

	    String trimmed = accountNo.trim();

	    if (trimmed.length() < 8) {
	        String padded = String.format("%08d", Long.parseLong(trimmed));
	        return padded.replaceFirst("^0+", "");
	    }

	    return trimmed;
	}
