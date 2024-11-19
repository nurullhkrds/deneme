@Override
public boolean validate(EventData events, DisplayContext dc, ConversationContextManager cc) {
    // Önce üst sınıftaki validasyonları çağırıyoruz
    boolean isValidated = super.validate(events, dc, cc);
    
    // Eğer mevcut validasyon başarısızsa doğrudan false döneriz
    if (!isValidated) {
        return false;
    }

    // ALAN sütunundaki gerekli değerlerin varlığını kontrol ediyoruz
    List<String> missingValues = getMissingValuesInColumn(this, "ALAN", cc);
    if (!missingValues.isEmpty()) {
        showValidationErrorMessage("ALAN sütununda eksik değerler var: " + String.join(", ", missingValues), dc);
        return false; // Eksik değerler nedeniyle validasyon başarısız
    }

    // Eğer üst sınıf validasyonu ve ALAN kontrolü başarılıysa true döneriz
    return true;
}

/**
 * Belirli bir sütunda eksik olan gerekli değerleri kontrol eder ve döndürür.
 */
private List<String> getMissingValuesInColumn(AbstractListViewController<?> controller, String columnName, ConversationContextManager cc) {
    ITable listView = controller.getListView();
    List<String> requiredValues = new ArrayList<String>();
    requiredValues.add("DOVIZ");
    requiredValues.add("ISLEMTUTAR");
    requiredValues.add("KKMASRAF");
    requiredValues.add("BSMV");
    requiredValues.add("TOPLAMTUTAR");

    int columnIndex = -1;

    // Sütun adını bularak indeksini alıyoruz
    String[] columnFormats = listView.getFormat().split(";");
    for (int i = 0; i < columnFormats.length; i++) {
        if (listView.getLocalizedTitle(i, cc).equalsIgnoreCase(columnName)) {
            columnIndex = i;
            break;
        }
    }

    if (columnIndex == -1) {
        return requiredValues; // ALAN sütunu bulunamadıysa tüm değerler eksik sayılır
    }

    // ALAN sütunundaki değerleri kontrol et
    List<String> foundValues = new ArrayList<String>();
    for (int i = 0; i < listView.getNumRow(); i++) {
        String cellValue = StringUtils.suppressNull(listView.getCell(i, columnIndex));
        if (requiredValues.contains(cellValue)) {
            foundValues.add(cellValue);
        }
    }

    // Eksik değerleri bul
    List<String> missingValues = new ArrayList<String>(requiredValues);
    missingValues.removeAll(foundValues);

    return missingValues;
}

/**
 * Validasyon hatası mesajını kullanıcıya gösterir.
 */
private void showValidationErrorMessage(String message, DisplayContext dc) {
    MessagesUtil.addError(message, null); // Hata mesajını ekrana yazdır
}
