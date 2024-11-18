  protected IUserButton getBtnPG1111TransactionReceipt(DisplayContext dc) {
        return (IUserButton) dc.get(DCKEY_BTNPG1111TRANSACTIONRECEIPT);
    }
	@SuppressWarnings({ "rawtypes" })
	public void onPopupConfirmClicked(EventData events, DisplayContext dc, ConversationContextManager cc) {
		String dialogID = Session.CURRENT_LISTVIEW.getSessionValue(cc, String.class);
		IBasicFormActions controller = getLastListController(cc);
		if (controller != null && controller.validate(events, dc, cc) == false)
			return;// listede hatalar var, validataion methodunda ilgili mesaj ekrana basıldı

		IHTMLWidget widget = ((IHTMLWidget) dc.get(BUTTON_PREFIX + dialogID));
		if (controller instanceof AbstractListViewController && ((AbstractListViewController) controller).getRowCount() == 0) {
			widget.addStyle("btn-info");
		} else if (controller instanceof AbstractListViewController) {
			if (widget.hasStyle("btn-info") == true) {
				widget.removeStyle("btn-info");// TODO
			}
			widget.addStyle("btn");

		}

		IContainer dialog = getDialogByID(dialogID, events, dc, cc);
		dialog.setVisible(false);

	}
