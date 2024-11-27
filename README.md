   if ("btnPG1111TransactionReceiptPopupDelete".equals(buttonId)) {
                boolean isValidated = controller.validate(events, dc, cc);
                if (!isValidated) {
                    // Hata mesajı zaten validate metodunda gönderilmiş olmalı, burada tekrarlanmasına gerek yok
                    return;
                }
            }


@Override
public boolean validate(EventData events, DisplayContext dc, ConversationContextManager cc) {
    String buttonId = events.getUserEventWidgetID();

    // Sil butonu için özel kontrol
    if ("btnPG1111TransactionReceiptPopupDelete".equals(buttonId)) {
        boolean isDeleted = validateDelete(events, dc, cc);
        if (!isDeleted) {
            return false;
        }
    }

    // Önce üst sınıftaki validasyonları uygular ona göre döner
    boolean isValidated = super.validate(events, dc, cc);
    if (!isValidated) {
        return false;
    }

    if ("btnPG1111TransactionReceiptPopupConfirm".equals(buttonId)) {
        ITable listView = getListView();
        if (listView == null) {
            return false;
        }

        List<String> missingValues = getMissingValuesInColumn(listView, "ALAN", cc);
        if (!missingValues.isEmpty()) {
            showValidationErrorMessage("ALAN sütununda zorunlu değerlerden biri veya birden fazlası eksik: " + String.join(", ", missingValues), events);
            return false; // Eksik değerler nedeniyle validasyon başarısız olur..
        }
    }

    // Eğer tüm validasyonlar başarılıysa true döneriz
    return true;
}

private boolean validateDelete(EventData events, DisplayContext dc, ConversationContextManager cc) {
    ITable listView = getListView();
    String selectedIndex = listView.getValue();
    if (StringUtils.hasText(selectedIndex) && !selectedIndex.equals("-1")) {
        int columnIndex = findColumnIndex(listView, "ALAN", cc);
        if (columnIndex == -1) {
            showValidationErrorMessage("ALAN sütunu bulunamadı.", events);
            return false;
        }

        String cellValue = StringUtils.suppressNull(listView.getCell(Integer.parseInt(selectedIndex), columnIndex));
        if ("TOPLAMTUTAR".equals(cellValue) || "KKMASRAF".equals(cellValue)) {
            showValidationErrorMessage("TOPLAMTUTAR veya KKMASRAF Alan sütunları silinemez!", events);
            return false; // Silme işlemi durdurulur
        }
    } else {
        showValidationErrorMessage("Önce listeden bir kayıt seçiniz.", events);
        return false;
    }
    return true;
}
