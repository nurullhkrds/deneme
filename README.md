@Override
	public boolean validate(EventData events, DisplayContext dc, ConversationContextManager cc) {
	    // Önce üst sınıftaki validasyonları çağırıyoruz
	    boolean isValidated = super.validate(events, dc, cc);

	    // Eğer mevcut validasyon başarısızsa doğrudan false döneriz
	    if (!isValidated) {
	        return false;
	    }


	   
	    List<String> missingValues = getMissingValuesInColumn((AbstractListViewController) cc, "Alan", cc);
		 if (!missingValues.isEmpty()) {
		      showValidationErrorMessage("ALAN sütununda zorunlu değerlerden biri veya birden fazlası eksik: " + String.join(", ", missingValues), events);
		      return;
		  }

	    // Eğer üst sınıf validasyonu ve ALAN kontrolü başarılıysa true döneriz
	    return true;
	}
	
	
	
	/**
	 * Belirli bir sütunda gerekli değerlerin varlığını kontrol eder.
	 */
	private List<String> getMissingValuesInColumn(AbstractListViewController<?> controller, String columnName, ConversationContextManager cc) {
	    ITable listView = controller.getListView();
	    List<String> requiredValues = new ArrayList<String>();
	    requiredValues.add("DOVIZ");
	    requiredValues.add("ISLEMTUTAR");
	    requiredValues.add("KKMASRAF");
	    requiredValues.add("BSMV");
	    requiredValues.add("TOPLAMTUTAR");

	    int columnIndex = -1;

	    // Sütun adını bularak indeksini buluyoruz...
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

	    // ALAN sütunundaki değerleri kontrol ediyoruz...
	    List<String> foundValues = new ArrayList<String>();
	    for (int i = 0; i < listView.getNumRow(); i++) {
	        String cellValue = StringUtils.suppressNull(listView.getCell(i, columnIndex));
	        if (requiredValues.contains(cellValue)) {
	            foundValues.add(cellValue);
	        }
	    }

	    // Eksik değerleri bulup return ediyrouz.
	    List<String> missingValues = new ArrayList<String>(requiredValues);
	    missingValues.removeAll(foundValues);

	    return missingValues;
	}
	
	private void showValidationErrorMessage(String message, EventData events) {
	    MessagesUtil.addError(message, events);
	}
	
