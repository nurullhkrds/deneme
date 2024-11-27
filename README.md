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
        List<String> prohibitedValues = Arrays.asList("TOPLAMTUTAR", "KKMASRAF", "DÖVİZ"); // Özel durumlar listesi
        if (prohibitedValues.contains(cellValue)) {
            String message = cellValue + " alanı zorunludur, silinemez!";
            showValidationErrorMessage(message, events);
            return false; // Silme işlemi durdurulur
        }
    } else {
        showValidationErrorMessage("Önce listeden bir kayıt seçiniz.", events);
        return false;
    }
    return true;
}
