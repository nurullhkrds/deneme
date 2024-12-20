   public enum Property {
        NO_CHANGE_FOR_SAVING ("NO_CHANGE_FOR_SAVING") //= Kaydedilecek değişiklik yok.
        ,
        OPERATION_COMPLETED ("OPERATION_COMPLETED") //= İşlem tamamlandı.
        ,
        CONFIRM_FOR_DELETE ("CONFIRM_FOR_DELETE")//= Kaydı silmek istediğinizden emin misiniz?
        ,
        OPERATIONS_CANCELED ("OPERATIONS_CANCELED")//= İşlem(ler) iptal edildi.
        ,
        CONFIRM_FOR_OPERATION ("CONFIRM_FOR_OPERATION")//= Kaydedilmemiş değişiklikleriniz var, devam etmek istediğinize emin misiniz?
        ,
        FIRSTLY_SAVE_OR_CANCEL ("FIRSTLY_SAVE_OR_CANCEL")//=Öncelikle değişiklikleri kayıt veya iptal ediniz.
        ,
        ONLY_NUMBER ("ONLY_NUMBER")//=Bu alana sadece sayı girişi yapılabilir.
        ,
        ONLY_NEGATIVE_NUMBER ("ONLY_NEGATIVE_NUMBER")//= Bu alana sadece sıfır veya negatif sayılar girilebilir.
        ,
        REQUIRED_FIELD ("REQUIRED_FIELD")//=Değer girişi zorunlu alana ( %s ) 
        ,
        NO_DATA_FOR_CORPORATE_DETAIL ("NO_DATA_FOR_CORPORATE_DETAIL")//=Kuruma ait detay bilgisi bulunamadı.
        ,
        CREDIT_CARD_MESSAGE ("CREDIT_CARD_MESSAGE")//=YKBFIRMAKOD, KKMASRAFKODU ve KKUYEISYERI alanlarının tamamı dolu olmalıdır.
        ,
        ISKN_ALLOWED ("ISKN_ALLOWED")//=ISKN Hesap Tanımlanabilsin
        ,
        ROW_ERROR ("ROW_ERROR")//= %s. Satırda Hata : %s 
        ,
        ACCOUNT_NUMBER_CHANGED ("ACCOUNT_NUMBER_CHANGED")//=Hesap No Listesi %s. satır için değiştirilmiş. Başka kayıt için düzenleme yapmak için önce değişiklikleri kaydemeniz gerekmektedir...
        ,
        ACCOUNT_TYPE_CHANGED ("ACCOUNT_TYPE_CHANGED")//=Hesap Tipi Listesi %s. satır için değiştirilmiş. Başka kayıt için düzenleme yapmak için önce değişiklikleri kaydemeniz gerekmektedir...
        ,
        SCAN_LIST_CHANGED ("SCAN_LIST_CHANGED")//=Tarama Detayı Listesi %s. satır için değiştirilmiş. Başka kayıt için düzenleme yapmak için önce değişiklikleri kaydemeniz gerekmektedir...
        ,
        AUTO_PROCESS ("AUTO_PROCESS")//= %s Tüm Hesaptan Otomatik Process'ler İçindir...
        ,
        NO_DATA_FOUND ("NO_DATA_FOUND")//=Sorgu koşullarına uygun kayıt bulunamadı...
        ,
        SCAN_REQUIRED ("SCAN_REQUIRED")// =Tarama Olmadan Detay Belirtemezsiniz.
        ,
        REQUIRED_FIELD_BYROW_NUM ("REQUIRED_FIELD_BYROW_NUM") // %s. satırdaki '%s' alanına değer girişi zorunludur..." 
        ,
        PRE_SCAN_MESSAGE ("PRE_SCAN_MESSAGE")//=Bu alana girilen değer Ön Tarama değerini aşamaz.
        ,
        POST_SCAN_MESSAGE ("POST_SCAN_MESSAGE")//=Bu alana girilen değer Son Tarama değerini aşamaz.
        ,
        SELECT_DETAIL_RECORD ("SELECT_DETAIL_RECORD")//=Listeden kayıt seçmelisiniz	
        , 
        INDIVIDUAL_AUTOPAY_COULD_NOT_SET("INDIVIDUAL_AUTOPAY_COULD_NOT_SET") //=Bireysel pay Otomatik(01), Otomatik(03), Otomatik(05), Otomatik(06), Otomatik(GünSonu) turuna set edilemez.
        ;
        String propertyKey;

        private Property(String propertyKey) {
            this.propertyKey = propertyKey;
        }

        public String toString() {
            return propertyKey;
        }

        public String getValue(PG1112_CorporateDetail page, ConversationContextManager cc) {
            return page.getProperty(propertyKey, cc);
        }
    }    public void onbtnMIOkPG1112Clicked(EventData events, DisplayContext dc, ConversationContextManager cc) {
        if (!validateForDdsDiscount(events, dc, cc)) {
            return;
        }
        try {
            if(blockOtoPay09IndividualYes(dc, cc, events)){
                return;
            }
            
            if (ObjectUtils.objToInteger(getLstvMIPG1112(dc).getValue()).equals(-1) || getLstvMIPG1112(dc).getNumRow() == 0) {
                MessagesUtil.addError(Property.NO_CHANGE_FOR_SAVING.getValue(this, cc), events);
                return;
            }

            if (!NotificationParametersParser.isTypeBackAfterApproval(cc) || Session.HAS_SAVE_ERROR.getSessionValue(cc, Boolean.class)) {
                Integer rowNum = ObjectUtils.objToInteger(getLstvMIPG1112(dc).getCell(ObjectUtils.objToInteger(getLstvMIPG1112(dc).getValue()), MainListviewColumns.Line.getColoumnVal()));
                AutoCorporateDetailDTO dto = mainScreen2AutoCorporateDetailDTO(rowNum, dc, cc);
                updateSessionCorporateDetailList(cc, dto);

                if (hasPageChange(cc) == false) {//NO_CHANGE_FOR_SAVING
                    String message = Property.NO_CHANGE_FOR_SAVING.getValue(this, cc);
                    MessagesUtil.addError(Property.NO_CHANGE_FOR_SAVING.getValue(this, cc), events);
                    return;
                }
            }
