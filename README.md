@SuppressWarnings({ "rawtypes" })
public void onPopupDeleteClicked(EventData events, DisplayContext dc, ConversationContextManager cc) {
    try {
        AbstractListViewController controller = (AbstractListViewController) getLastListController(cc);
        String selectedIndex = controller.getListView().getValue();

        if (StringUtils.hasText(selectedIndex) && !selectedIndex.equals("-1")) {
            MyRowObject selectedRow = controller.getRowData(Integer.parseInt(selectedIndex));
            if (selectedRow.getField("Döviz") != null) { // Döviz Cinsi alanını kontrol ediyor
                MessagesUtil.addError("Döviz Cinsi alanı silinemez!", events);
                return;
            }

            String confirmMsg = "Kaydı silmek istediğinizden emin misiniz?";
            if (!MessagesUtil.showApprovalMessage(confirmMsg, "onPopupDeleteConfirm", "onPopupDeleteConfirm", events, dc, cc))
                return;
            controller.deleteRow(Integer.parseInt(selectedIndex));
        } else {
            MessagesUtil.addError("Önce listeden bir kayıt seçiniz.", events);
        }
    } catch (Exception e) {
        MessagesUtil.showExceptionMessage(dc, events, e);
    }
}
