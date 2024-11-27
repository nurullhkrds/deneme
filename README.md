private boolean validateDelete(EventData events, DisplayContext dc, ConversationContextManager cc) {
	    ITable listView = getListView();
	    String selectedIndex = listView.getValue();
	    if (StringUtils.hasText(selectedIndex) && !selectedIndex.equals("-1")) {
	        int columnIndex = findColumnIndex(listView, "ALAN", cc);
	        if (columnIndex == -1) {
	            showValidationErrorMessage("ALAN sütunu bulunamadı.", events);
	            return false;
	        }

	        String cellValue = StringUtils.suppressNull(listView.getCell(Integer.parseInt(selectedIndex), columnIndex));
	        if ("TOPLAMTUTAR".equals(cellValue) || "KKMASRAF".equals(cellValue)) {
	            showValidationErrorMessage("TOPLAMTUTAR veya KKMASRAF Alan sütunları silinemez!", events);
	            return false; // Silme işlemi durdurulur
	        }
	    } else {
	        showValidationErrorMessage("Önce listeden bir kayıt seçiniz.", events);
	        return false;
	    }
	    return true;
	}
