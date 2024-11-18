@SuppressWarnings({ "rawtypes" })
public void onPopupConfirmClicked(EventData events, DisplayContext dc, ConversationContextManager cc) {
    String dialogID = Session.CURRENT_LISTVIEW.getSessionValue(cc, String.class);
    IBasicFormActions controller = getLastListController(cc);

    // Controller validasyonu
    if (controller != null && controller.validate(events, dc, cc) == false) {
        return; // Validasyon hatası
    }

    // ALAN sütunundaki gerekli değerlerin varlığı kontrol ediliyor
    if (!validateRequiredValuesInColumn((AbstractListViewController) controller, "ALAN", cc)) {
        showValidationErrorMessage("ALAN sütununda gerekli değerlerden biri eksik: DOVIZ, ISLEMTUTAR, KKMASRAF, BSMV, TOPLAMTUTAR", dc);
        return; // Eksik değerler nedeniyle işlem kesiliyor
    }

    // Widget işlemleri
    IHTMLWidget widget = ((IHTMLWidget) dc.get(BUTTON_PREFIX + dialogID));
    if (controller instanceof AbstractListViewController && ((AbstractListViewController) controller).getRowCount() == 0) {
        widget.addStyle("btn-info");
    } else if (controller instanceof AbstractListViewController) {
        if (widget.hasStyle("btn-info")) {
            widget.removeStyle("btn-info");
        }
        widget.addStyle("btn");
    }

    // Popup kapatma
    IContainer dialog = getDialogByID(dialogID, events, dc, cc);
    dialog.setVisible(false);
}

/**
 * Belirli bir sütunda gerekli değerlerin varlığını kontrol eder.
 */
private boolean validateRequiredValuesInColumn(AbstractListViewController<?> controller, String columnName, ConversationContextManager cc) {
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
        return false; // ALAN sütunu bulunamadı
    }

    // ALAN sütunundaki değerleri kontrol et
    List<String> foundValues = new ArrayList<String>();
    for (int i = 0; i < listView.getNumRow(); i++) {
        String cellValue = StringUtils.suppressNull(listView.getCell(i, columnIndex));
        if (requiredValues.contains(cellValue)) {
            foundValues.add(cellValue);
        }
    }

    // Gerekli tüm değerler bulundu mu?
    return foundValues.containsAll(requiredValues);
}

/**
 * Validasyon hatası mesajını kullanıcıya gösterir.
 */
private void showValidationErrorMessage(String message, DisplayContext dc) {
    MessagesUtil.addError(message, null); // Hata mesajını ekrana yazdır
}
