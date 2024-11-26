 public void onChangeListViewCellValue(EventData events, DisplayContext dc, ConversationContextManager cc) {

        try {
            Integer mainRow = ObjectUtils.objToInteger(getLstvMIPG1112(dc).getValue());
            Integer lastRow = Session.CORPORATE_DETAILS_LAST_UPDATED_ROW.getSessionValue(cc, Integer.class);
            if (events.getUserEventWidgetID().equals(DCKEY_LSTVMIPG1112)) {
                //main list edited
                String data = getLstvMIPG1112(dc).getLastUpdatedCellNumber();
                String[] selectedCellDetail = data.split(",");
                int cellRow = Integer.parseInt(selectedCellDetail[0]);
                int cellColumn = Integer.parseInt(selectedCellDetail[1]);

                //seçili olmayan bir satırda, seç kolonu haricinde bir değeri değiştirirse hata verip,
                //kullanıcının değiştirdiği datayı eski haline almamız gerekiyor
                if (((lastRow != null && cellRow != lastRow) || cellRow != mainRow) && cellColumn != 1) {
                    MessagesUtil.addError("Değişiklik yapmadan önce, değişiklik yapılacak satır seçilmelidir!", events);
                    String columnOldValue = getColumnValueFromSession(cellRow, cellColumn, events, cc);
                    getLstvMIPG1112(dc).setCell(cellRow, cellColumn, columnOldValue);//kolon değerini eski haline aldık                         
                    getLstvMIPG1112(dc).setValue(lastRow == null ? String.valueOf(lastRow) : lastRow.toString());

                    return;
                }

                validateMainListCell(events, dc, cc);
            } else if (events.getUserEventWidgetID().equals(DCKEY_LSTVCAPG1112)) {
                //account number list edited
                validateAccountNumberListCell(events, dc, cc);
                Session.ACCOUNT_NUMBERS_CHANGED.setValue(true, cc);
                Session.ACCOUNT_NUMBERS_CHANGED_ROW.setValue(mainRow, cc);
            } else if (events.getUserEventWidgetID().equals(DCKEY_LSTVATPG1112)) {
                //account type list edited
                validateAccountTypeListCell(events, dc, cc);
                Session.ACCOUNT_TYPE_CHANGED.setValue(true, cc);
                Session.ACCOUNT_TYPE_CHANGED_ROW.setValue(mainRow, cc);
            } else if (events.getUserEventWidgetID().equals(DCKEY_LSTVSCPG1112)) {
                //scan list edited
                validateScanListCell(events, dc, cc);
                Session.SCAN_INFO_CHANGED.setValue(true, cc);
                Session.SCAN_INFO_CHANGED_ROW.setValue(mainRow, cc);

            }
        } catch (Exception e) {
            MessagesUtil.showExceptionMessage(dc, events, e);
        }

    }









ve 



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


ve 



 private Boolean validateListCell(Integer statusColumnNo, Integer[] requiredColoumns, Integer[] columnLengths, Integer[] intColumns, Integer[] negativeIntColoumns, ITable lstCurrent,
            Map<Integer, String> caseType, EventData events, DisplayContext dc, ConversationContextManager cc) {
        Boolean retval = true;
        String data = lstCurrent.getLastUpdatedCellNumber();
        String[] str = data.split(",");
        int cellRow = Integer.parseInt(str[0]);
        int cellColumn = Integer.parseInt(str[1]);

        String cellStatus = lstCurrent.getCell(cellRow, statusColumnNo);
        String cellValue = lstCurrent.getCell(cellRow, cellColumn);

        if (caseType != null) {
            if (caseType.containsKey(cellColumn)) {
                if (caseType.get(cellColumn).equals("U"))
                    lstCurrent.setCell(cellRow, cellColumn, cellValue.toUpperCase(new Locale("TR")));
                else if (caseType.get(cellColumn).equals("L"))
                    lstCurrent.setCell(cellRow, cellColumn, cellValue.toLowerCase(new Locale("TR")));
            }
        }

        if (requiredColoumns != null) {
            for (int i = 0; i < requiredColoumns.length; i++) {
                if (cellColumn == requiredColoumns[i]) {
                    if (StringUtils.hasNotText(cellValue)) {
                        lstCurrent.setCell(cellRow, cellColumn, "");
                        String message = Property.REQUIRED_FIELD.getValue(this, cc);
                        message = String.format(message, lstCurrent.getLocalizedTitle(cellColumn, cc));
                        MessagesUtil.addError(message, events);
                        return false;
                    }
                }
            }
        }

        if (intColumns != null) {
            for (int i = 0; i < intColumns.length; i++) {
                if (cellColumn == intColumns[i]) {
                    if (isNumeric(cellValue, events, dc, cc) == false) {
                        lstCurrent.setCell(cellRow, cellColumn, "");
                        retval = false;
                    }
                }
            }

        }

        if (negativeIntColoumns != null) {
            for (int i = 0; i < negativeIntColoumns.length; i++) {
                if (cellColumn == negativeIntColoumns[i]) {
                    if (isNumeric(cellValue, events, dc, cc) == false) {
                        lstCurrent.setCell(cellRow, cellColumn, "");
                        retval = false;
                    } else if (ObjectUtils.objToInteger(cellValue).compareTo(0) > 0) {
                        lstCurrent.setCell(cellRow, cellColumn, "");
                        retval = false;
                        String message = Property.ONLY_NEGATIVE_NUMBER.getValue(this, cc);
                        MessagesUtil.addError(message, events);
                    }
                }
            }
        }

        if (cellValue.length() > columnLengths[cellColumn]) {
            lstCurrent.setCell(cellRow, cellColumn, "");
            MessagesUtil.addError(lstCurrent.getLocalizedTitle(cellColumn, cc) + " alanına en fazla " + columnLengths[cellColumn] + " karakter girebilirsiniz.", events);
            retval = false;
        }

        if (cellStatus.contains(Constants.INSERTED_CELL.toString())) {//sonradan eklenen bir cell mi
            lstCurrent.setCell(cellRow, statusColumnNo, Constants.INSERTED_CELL.toString());
        } else {//sonradan eklenmediyse update edilecektir
            lstCurrent.setCell(cellRow, statusColumnNo, Constants.UPDATED_CELL.toString());
        }
        return retval;
    }
