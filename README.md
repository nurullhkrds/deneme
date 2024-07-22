public static String currencyConverter(String currency) {
        if (currency == null || currency.equals(BillTransactionConstant.TL_CURRENCY) || currency.equals(BillTransactionConstant.TRY_CURRENCY)) {
            return BillTransactionConstant.YTL_CURRENCY;
        }
        return currency;
    }

    public static String generateCreditCardProvisionRequestId(String channelCode,boolean isDummyMerchant) {

        StringBuilder requestIdBuilder = new StringBuilder();

        requestIdBuilder.append(BillTransactionConstant.BILLPRE);
        if(isDummyMerchant) { 
        	requestIdBuilder.append(BillTransactionConstant.CreditCardProvision.CREDIT_CARD_TRANSACTION_TYPE_DUMMY);
        }else {
        	requestIdBuilder.append(BillTransactionConstant.CreditCardProvision.CREDIT_CARD_TRANSACTION_TYPE_REAL);
        }
        
        requestIdBuilder.append(channelCode);
        String uuidAsString = UUID.randomUUID().toString().replace("-", "");
        requestIdBuilder.append(uuidAsString);

        return requestIdBuilder.toString();

    }

    public static String maskCreditCardNo(String cardNo, String maskChar, Integer maskCharLength) {
        return StringUtils.substring(cardNo, 0, 6) +
                StringUtils.repeat(maskChar, maskCharLength) +
                StringUtils.substring(cardNo, cardNo.length() - 4);
    }
