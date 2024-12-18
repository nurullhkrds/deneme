   private Boolean validateMainListCell(EventData events, DisplayContext dc, ConversationContextManager cc) throws FWScopeException, InstantiationException, IllegalAccessException {
        Boolean retval = true;

        Integer statusColumn = MainListviewColumns.STATU.getColoumnVal();
        Integer[] length = { 100000, 100000, 100000, 100000, 100000, 100000, 4, 3, 100000, 5, 5, 100, 100000, 100000, 100000, 100000, 100000 };// kolonların maxlengthleri ne kadar
        Integer[] negativeIntColoumns = { MainListviewColumns.ONTARAMA.getColoumnVal() };
        Integer[] intColoumns = { MainListviewColumns.SONTARAMA.getColoumnVal() };
        Integer[] requiredColoumns = { MainListviewColumns.CTYPE.getColoumnVal(), MainListviewColumns.DTYPE.getColoumnVal(), MainListviewColumns.ODMTIP.getColoumnVal(),
                MainListviewColumns.ONTARAMA.getColoumnVal(), MainListviewColumns.SONTARAMA.getColoumnVal(), MainListviewColumns.TEXT.getColoumnVal() };

        ITable lstCurrent = getLstvMIPG1112(dc);
        if (validateListCell(statusColumn, requiredColoumns, length, intColoumns, negativeIntColoumns, lstCurrent, null, events, dc, cc) == false) {
            return false;
        }

        //Extra Controls
        String data = lstCurrent.getLastUpdatedCellNumber();
        String[] str = data.split(",");
        int cellRow = Integer.parseInt(str[0]);
        int cellColumn = Integer.parseInt(str[1]);
        try {
            if (cellColumn == MainListviewColumns.ODMTIP.getColoumnVal()) {
                String pymType = getPymTypeComboKeyByComboValue(cc, getLstvMIPG1112(dc).getCell(cellRow, cellColumn));
                setMainPageEnable(events, dc, cc, cellRow);
                String active = getLstvMIPG1112(dc).getCell(cellRow, MainListviewColumns.AKTIF.getColoumnVal());
                active = "Evet".equals(active) ? "A" : "H";
                if ("E".equals(Session.AUTO_CORPORATE_DTO.getSessionValue(cc, AutoCorporateModelDTO.class).getMultipleAccount())) {
                    if (pymType.matches("\\d")) {
                        lstCurrent.setCellEnabled(cellRow, MainListviewColumns.BIREYSEL.getColoumnVal());
                    } else {
                        lstCurrent.setCellDisabled(cellRow, MainListviewColumns.BIREYSEL.getColoumnVal());
                    }
                }
                if (checkWebService(pymType, active, events, dc, cc) == false) {
                    retval = false;
                    int rowNum = ObjectUtils.objToInteger(lstCurrent.getCell(cellRow, MainListviewColumns.Line.getColoumnVal()));
                    String oldPayment = getPymTypeComboKeys(cc).get(getAutoCorporateDetailDtoByRowNum(cc, rowNum).getPartialPayment());
                    lstCurrent.setCell(cellRow, cellColumn, oldPayment);
                }

                if ("X,Y,Z".contains(pymType)) {
                    if (isCreditCard(events, dc, cc) == false) {
                        retval = false;
                        lstCurrent.setCell(cellRow, cellColumn, "");
                    }
                }
            } else if (cellColumn == MainListviewColumns.AKTIF.getColoumnVal()) {
                String pymType = getPymTypeComboKeyByComboValue(cc, getLstvMIPG1112(dc).getCell(cellRow, cellColumn));
                setMainPageEnable(events, dc, cc, cellRow);
                String active = getLstvMIPG1112(dc).getCell(cellRow, MainListviewColumns.AKTIF.getColoumnVal());
                active = "Evet".equals(active) ? "A" : "H";
                String columnOldValue = getColumnValueFromSession(cellRow, cellColumn, events, cc);
             
                
                // Yeni Uyarı Mesajı Ekleme
                if ("A".equals(active)) {
                    // Dekont 
               
                	 List<AutoTransactionReceiptModelDTO> receiptList=listData(Session.CORPORATE.getSessionValue(cc, String.class),Session.PRODUCT.getSessionValue(cc, String.class));
                    if (receiptList != null) {
                    	
                    	List<String> requiredValues = new ArrayList<String>();
                	    requiredValues.add("DOVIZ");
                	    requiredValues.add("ISLEMTUTAR");
                	    requiredValues.add("KKMASRAF");
                	    requiredValues.add("BSMV");
                	    requiredValues.add("TOPLAMTUTAR");
                	    List<String> foundValues =  new ArrayList<String>();

                        for (AutoTransactionReceiptModelDTO field : receiptList) {
                            if (requiredValues.contains(field.getField())) {
                                foundValues.add(field.getField());
                            }
                        }

                        requiredValues.removeAll(foundValues);
                        if (!requiredValues.isEmpty()) {
                            MessagesUtil.addError(String.join(", ", requiredValues) + " alanları zorunludur.", events);
                            retval = false;
                            lstCurrent.setCell(cellRow, cellColumn, columnOldValue);//kolon değerini eski haline getirildi...                       


                        }
                                        
                    } 

                }


                
                if (checkWebService(pymType, active, events, dc, cc) == false) {
                    retval = false;
                    lstCurrent.setCell(cellRow, cellColumn, "0");
                }
            }
        } catch (Exception e) {
            MessagesUtil.showExceptionMessage(dc, events, e);
            retval = false;
            lstCurrent.setCell(cellRow, cellColumn, "");
        }

        if (retval == true) {
            Integer rowNum = ObjectUtils.objToInteger(getLstvMIPG1112(dc).getCell(cellRow, MainListviewColumns.Line.getColoumnVal()));
            AutoCorporateDetailDTO dto = mainScreen2AutoCorporateDetailDTO(rowNum, dc, cc);
            updateSessionCorporateDetailList(cc, dto);
        }

        return retval;
    }
