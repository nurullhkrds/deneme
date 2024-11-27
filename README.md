	@SuppressWarnings({ "rawtypes" })
	public void onPopupDeleteClicked(EventData events, DisplayContext dc, ConversationContextManager cc) {
		try {
			AbstractListViewController controller = ((AbstractListViewController) getLastListController(cc));
			String selectedIndex = controller.getListView().getValue();

			if (StringUtils.hasText(selectedIndex) && selectedIndex.equals("-1") == false) {
				String confirmMsg = "Kaydı silmek istediğinizden emin misiniz ?";
				if (MessagesUtil.showApprovalMessage(confirmMsg, "onPopupDeleteConfirm", "onPopupDeleteConfirm", events, dc, cc) == false)
					return;
				((AbstractListViewController) getLastListController(cc)).deleteRow(Integer.parseInt(selectedIndex));
			} else {
				MessagesUtil.addError("Önce listeden bir kayıt seçiniz.", events);
			}
		} catch (Exception e) {
			MessagesUtil.showExceptionMessage(dc, events, e);
		}
	}



bu sil butonu silme işlemi 




	public void onPopupCancelClicked(EventData events, DisplayContext dc, ConversationContextManager cc) {
		try {
			String dialogID = Session.CURRENT_LISTVIEW.getSessionValue(cc, String.class);
			AbstractListViewController controller = (AbstractListViewController) getLastListController(cc);
			if (controller.isChanged(events, dc, cc)) {
				if (MessagesUtil.showApprovalMessage("Yapılan işlem(ler) iptal edilecektir, emin misiniz?", "onPopupCancelConfirm", "onPopupCancelConfirm", events, dc,
						cc) == false) {
					return;
				}
			}
			controller.removeAll();
			IContainer dialog = getDialogByID(dialogID, events, dc, cc);
			dialog.setVisible(false);
		} catch (Exception e) {
			MessagesUtil.showExceptionMessage(dc, events, e);
		}
	}


bu da vazgeç butonu vazgeç işlemi 





	@SuppressWarnings({ "rawtypes" })
	public void onPopupAddClicked(EventData events, DisplayContext dc, ConversationContextManager cc) {
		try {
			((AbstractListViewController) getLastListController(cc)).addEmptyRow(events, dc, cc);

			String listId = "lstPG1111" + Session.CURRENT_LISTVIEW.getSessionValue(cc, String.class) + "PopupListView";
			cc.remove("scrollDown", Scope.FW_CLIENT_CONVERSATION_SCOPE);
			cc.put("scrollDownList", listId, Scope.FW_CLIENT_CONVERSATION_SCOPE);
			cc.put("scrollDown", "YES", Scope.FW_CLIENT_CONVERSATION_SCOPE);

		} catch (Exception e) {
			MessagesUtil.showExceptionMessage(dc, events, e);
		}
	}

bu da ekle butonu ekleme işlemi







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
bu da tamam butonu











![image](https://github.com/user-attachments/assets/66a420e6-3995-414b-9f6d-a3f1279954b6)
