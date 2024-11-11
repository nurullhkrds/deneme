   if (StringUtils.hasText(accountNo)) {
                accountNo = PYMStringUtils.getAccount(accountNo);
                RequestCorporateDetailCheckAccountNumber request = new RequestCorporateDetailCheckAccountNumber();
                request.setAccountNo(accountNo);
                request.setMessagePre(mapPreMessage.get(cellColumn));
                request.setProduct(Session.PRODUCT.getSessionValue(cc, String.class)); 

  public static String getAccountStr(Integer accountNo) {
        if (accountNo == null)
            return null;
        return StringUtils.lpad(accountNo.toString(), 8, '0');
    }
        String accountNoStr = accountNo.toString();
        if(accountNoStr.length() == 8 || accountNoStr.length() == 9 ){
        	return accountNoStr;
        }
        return StringUtils.lpad(accountNoStr, 8, '0');
    } 
    YUKARDAKİ FRONTENDE ÇAĞIRDIĞIM YER AMA BANA  
    public static String getAccount(String account){
		if(isEmptyString(account)){
			return account;
		}
		
		account = getPotentialArea(account);
		
		if(account.length()<8){
			account = lPad(account, 8, ZERO);
		}
		
		return account;
	}
	
	private static String  getPotentialArea(String account){
		if(account.length()>9){
			account = account.substring(0,9);
		}
		return account.trim(); 
	}
