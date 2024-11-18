private boolean validateRequiredColumns(AbstractListViewController<?> controller, ConversationContextManager cc) {
    ITable listView = controller.getListView();
    List<String> requiredColumns = List.of("DOVIZ", "ISLEMTUTAR", "KKMASRAF", "BSMV", "TOPLAMTUTAR");

    for (String column : requiredColumns) {
        boolean columnExists = false;
        for (int i = 0; i < listView.getColumnCount(); i++) {
            String columnName = listView.getLocalizedTitle(i, cc);
            if (column.equalsIgnoreCase(columnName)) {
                columnExists = true;
                break;
            }
        }
        if (!columnExists) {
            return false; // Eksik sütun bulundu
        }
    }
    return true; // Tüm sütunlar mevcut
}
