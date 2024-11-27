	@Override
	public boolean validate(EventData events, DisplayContext dc, ConversationContextManager cc) {
		
	    // EventData üzerinden buton idsini alarak button bazlı knotol sağlıyoruz..
	    String buttonId = events.getUserEventWidgetID();

		
	    if ("btnPG1111TransactionReceiptPopupDelete".equals(buttonId)) {
	    	boolean isDeleted = validateDelete(events,dc,cc,buttonId);
	    	
	    	if (!isDeleted){
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



	@SuppressWarnings({ "rawtypes" })
	public void onPopupDeleteClicked(EventData events, DisplayContext dc, ConversationContextManager cc) {
		try {
			AbstractListViewController controller = ((AbstractListViewController) getLastListController(cc));
			String selectedIndex = controller.getListView().getValue();
		    String buttonId = events.getUserEventWidgetID();

			if (StringUtils.hasText(selectedIndex) && selectedIndex.equals("-1") == false) {
				
			    if ("btnPG1111TransactionReceiptPopupDelete".equals(buttonId)) {
			    	boolean isValidated=controller.validate(events, dc, cc);
			    	if(!isValidated){
			    		MessagesUtil.addError("TOPLAMTUTAR veya KKMASRAF Alan sütunları silinemez!", events);
			    		return;

			    	}
			    }
				
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
