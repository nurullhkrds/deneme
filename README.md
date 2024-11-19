@Override
	public boolean validate(EventData events, DisplayContext dc, ConversationContextManager cc) {
		
	    // Önce üst sınıftaki validasyonları uygular ona göre döner ....
	    boolean isValidated = super.validate(events, dc, cc);

	    if (!isValidated) {
	        return false;
	    }

	   
	    ITable listView = getListView();
	    if (listView == null) {
	        return false; 
	    }

	    String buttonId=events.getUserEventWidgetID();
	    if(buttonId.equals("btnPG1111TransactionReceiptPopupConfirm")){
	    	List<String> missingValues = getMissingValuesInColumn(listView, "ALAN", cc);
		    if (!missingValues.isEmpty()) {
		        showValidationErrorMessage("ALAN sütununda zorunlu değerlerden biri veya birden fazlası eksik: " + String.join(", ", missingValues), events);
		        return false; // Eksik değerler nedeniyle validasyon başarısız olur buradada..
		    }
	    	
	    }
	    

	    return true;
	}
	
