if ("A".equals(active)) {
    // Dekont tablosunda kontrol yap
    ParamModelDTO[] slipFields = Session.SLIP_FIELDS.getSessionValue(cc, ParamModelDTO[].class);
    if (slipFields == null) {
        MessagesUtil.addError("DEKONT tablosunda herhangi bir tanımınız yoktur.", events);
        retval = false;
    } else {
        boolean bsmvExists = false;
        boolean totalAmountExists = false;
        boolean kgfAccountExists = false;
        
        for (ParamModelDTO field : slipFields) {
            if ("BSMV".equals(field.getId())) {
                bsmvExists = true;
            } else if ("TOPLAMTUTAR".equals(field.getId())) {
                totalAmountExists = true;
            } else if ("KGFACCOUNT".equals(field.getId())) {
                kgfAccountExists = true;
            }
        }
        
        if (!bsmvExists || !totalAmountExists || !kgfAccountExists) {
            MessagesUtil.addError("BSMV, TOPLAMTUTAR, KGFACCOUNT alanları zorunludur.", events);
            retval = false;
        }
    }

    MessagesUtil.addWarning("Aktif kolonunu 'Evet' olarak değiştirdiniz. Bu değişikliğin sonuçlarını kontrol ediniz.", events);
}
