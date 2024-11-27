@Override
public boolean validate(EventData events, DisplayContext dc, ConversationContextManager cc) {
    boolean isValidated = super.validate(events, dc, cc);
    if (!isValidated) {
        return false;
    }

    String buttonId = events.getUserEventWidgetID();
    // Sil butonu için özel kontrol ekleniyor
    if ("btnPG1111TransactionReceiptPopupDelete".equals(buttonId)) {
        ITable listView = getListView();
        if (listView == null) {
            return false;
        }

        String selectedIndex = listView.getValue();
        if (StringUtils.hasText(selectedIndex) && !selectedIndex.equals("-1")) {
            int columnIndex = findColumnIndex(listView, "ALAN", cc);
            if (columnIndex == -1) {
                showValidationErrorMessage("ALAN sütunu bulunamadı.", events);
                return false;
            }

            String cellValue = StringUtils.suppressNull(listView.getCell(Integer.parseInt(selectedIndex), columnIndex));
            if ("DÖVİZ".equals(cellValue) || "TOPLAMALAN".equals(cellValue)) {
                showValidationErrorMessage("Döviz veya Toplam Alan sütunları silinemez!", events);
                return false; // Silme işlemi durdurulur
            }
        } else {
            showValidationErrorMessage("Önce listeden bir kayıt seçiniz.", events);
            return false;
        }
    }
    return true;
}



private int findColumnIndex(ITable listView, String columnName, ConversationContextManager cc) {
    String[] columnFormats = listView.getFormat().split(";");
    for (int i = 0; i < columnFormats.length; i++) {
        if (listView.getLocalizedTitle(i, cc).equalsIgnoreCase(columnName)) {
            return i;
        }
    }
    return -1; // Sütun bulunamadı
}
