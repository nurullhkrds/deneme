@Override
public boolean validate(EventData events, DisplayContext dc, ConversationContextManager cc) {
    // Önce üst sınıftaki validasyonları uygular ona göre döner
    boolean isValidated = super.validate(events, dc, cc);

    // Eğer üst sınıf validasyonu başarısızsa false döneriz
    if (!isValidated) {
        return false;
    }

    // EventData üzerinden buton kimliğini elde ediyoruz
    String buttonId = events.getUserEventWidgetID();
    
    // Sadece belirli buton tetiklendiyse ek validasyonu yapıyoruz
    if ("btnPG1111TransactionReceiptPopupConfirm".equals(buttonId)) {
        // listView nesnesine erişiyoruz
        ITable listView = getListView();
        if (listView == null) {
            return false; // Eğer listView mevcut değilse validasyon başarısız
        }

        // ALAN sütunundaki gerekli değerlerin varlığını kontrol ediyoruz
        List<String> missingValues = getMissingValuesInColumn(listView, "ALAN", cc);
        if (!missingValues.isEmpty()) {
            showValidationErrorMessage("ALAN sütununda zorunlu değerlerden biri veya birden fazlası eksik: " + String.join(", ", missingValues), events);
            return false; // Eksik değerler nedeniyle validasyon başarısız
        }
    }

    // Eğer tüm validasyonlar başarılıysa true döneriz
    return true;
}
