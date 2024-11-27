	@Override
	public boolean validate(EventData events, DisplayContext dc, ConversationContextManager cc) {
		
	    // Önce üst sınıftaki validasyonları uygular ona göre döner
	    boolean isValidated = super.validate(events, dc, cc);

	    if (!isValidated) {
	        return false;
	    }

	    // EventData üzerinden buton idsini alarak button bazlı knotol sağlıyoruz..
	    String buttonId = events.getUserEventWidgetID();
	    
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
