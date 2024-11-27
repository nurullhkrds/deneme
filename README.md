	
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

	
	
	
	/**
	 * Belirli bir sütunda gerekli değerlerin varlığını kontrol eder.
	 */
	private List<String> getMissingValuesInColumn(ITable listView, String columnName, ConversationContextManager cc) {
	    List<String> requiredValues = new ArrayList<String>();
	    requiredValues.add("DOVIZ");
	    requiredValues.add("ISLEMTUTAR");
	    requiredValues.add("KKMASRAF");
	    requiredValues.add("BSMV");
	    requiredValues.add("TOPLAMTUTAR");

	    int columnIndex = -1;

	    String[] columnFormats = listView.getFormat().split(";");
	    for (int i = 0; i < columnFormats.length; i++) {
	        if (listView.getLocalizedTitle(i, cc).equalsIgnoreCase(columnName)) {
	            columnIndex = i;
	            break;
	        }
	    }

	    if (columnIndex == -1) {
	        return requiredValues; 
	    }

	    // ALAN sütunundaki değerleri kontrol ediyoruz
	    List<String> foundValues = new ArrayList<String>();
	    for (int i = 0; i < listView.getNumRow(); i++) {
	        String cellValue = StringUtils.suppressNull(listView.getCell(i, columnIndex));
	        if (requiredValues.contains(cellValue)) {
	            foundValues.add(cellValue);
	        }
	    }

	    // Eksik değerleri bulup return ediyoruz
	    List<String> missingValues = new ArrayList<String>(requiredValues);
	    missingValues.removeAll(foundValues);

	    return missingValues;
	}
