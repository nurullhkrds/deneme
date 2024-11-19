
	public boolean validate(EventData events, DisplayContext dc, ConversationContextManager cc){
		boolean isValidated=true;
		for (int i = 0; i < listView.getNumRow(); i++) {
			isValidated=validateRow(i,events,dc,cc);
			if(!isValidated) break;
		}

		return isValidated;
	}
	
