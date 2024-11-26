private Boolean validateMainListCell(EventData events, DisplayContext dc, ConversationContextManager cc) throws FWScopeException, InstantiationException, IllegalAccessException {
    Boolean retval = true;

    // ... mevcut kodlar ...

    try {
        if (cellColumn == MainListviewColumns.ODMTIP.getColoumnVal()) {
            // mevcut kod...
        } else if (cellColumn == MainListviewColumns.AKTIF.getColoumnVal()) {
            String pymType = getPymTypeComboKeyByComboValue(cc, getLstvMIPG1112(dc).getCell(cellRow, cellColumn));
            setMainPageEnable(events, dc, cc, cellRow);
            String active = getLstvMIPG1112(dc).getCell(cellRow, MainListviewColumns.AKTIF.getColoumnVal());
            active = "Evet".equals(active) ? "A" : "H";

            // Yeni Uyarı Mesajı Ekleme
            if ("A".equals(active)) {
                MessagesUtil.addWarning("Aktif kolonunu 'Evet' olarak değiştirdiniz. Bu değişikliğin sonuçlarını kontrol ediniz.", events);
            }

            if (checkWebService(pymType, active, events, dc, cc) == false) {
                retval = false;
                lstCurrent.setCell(cellRow, cellColumn, "0");
            }
        }
    } catch (Exception e) {
        MessagesUtil.showExceptionMessage(dc, events, e);
        retval = false;
        lstCurrent.setCell(cellRow, cellColumn, "");
    }

    // ... mevcut kodlar ...

    return retval;
}
