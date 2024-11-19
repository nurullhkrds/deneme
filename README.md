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

	} ...


	@SuppressWarnings({ "rawtypes" })
	public IBasicFormActions getLastListController(ConversationContextManager cc) {
		return Session.CURRENT_CONTROLLER.getSessionValue(cc, IBasicFormActions.class);
	}


 
	@Override
	public boolean validate(EventData events, DisplayContext dc, ConversationContextManager cc) {
	    // Önce üst sınıftaki validasyonları çağırıyoruz
	    boolean isValidated = super.validate(events, dc, cc);

	    // Eğer mevcut validasyon başarısızsa doğrudan false döneriz
	    if (!isValidated) {
	        return false;
	    }

	    // Controller nesnesini alıyoruz
	    if (!(controller instanceof AbstractListViewController)) {
	        return false; // Eğer controller uygun türde değilse validasyon başarısız olur
	    }

	    // ALAN sütunundaki gerekli değerlerin varlığını kontrol ediyoruz
	    List<String> missingValues = getMissingValuesInColumn((AbstractListViewController<?>) controller, "ALAN", cc);
	    if (!missingValues.isEmpty()) {
	        showValidationErrorMessage("ALAN sütununda zorunlu değerlerden biri veya birden fazlası eksik: " + String.join(", ", missingValues), events);
	        return false; // Eksik değerler nedeniyle validasyon başarısız
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
