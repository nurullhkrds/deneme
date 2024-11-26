private IBasicFormActions getListViewController(String listViewName, EventData events, DisplayContext dc, ConversationContextManager cc) throws Exception {
		AutoCorporateModelDTO screen = screen2DTO(events, dc, cc);
		CorporateInquiryDTO dto = new CorporateInquiryDTO();
		dto.setCorporate(screen.getCorporate());
		dto.setProduct(screen.getProduct());
		// Factory?!
		String btnName = BUTTON_PREFIX + listViewName;

		if (btnName.equals(DCKEY_BTNPG1111TRANSACTIONRECEIPT)) {// Transaction Receipt aka Dekont
			ParamModelDTO[] fieldList = Session.SLIP_FIELDS.getSessionValue(cc, ParamModelDTO[].class);
			return new TransactionReceiptListController(listViewName, this, getListViewByID(listViewName, events, dc, cc), dto, fieldList);



ublic class TransactionReceiptListController extends AbstractCorporateDefinitionListController<AutoTransactionReceiptModelDTO> {
	private enum Fields{
		SELECTION,
		ROW,
		COLUMN,
		TYPE,
		LABEL,
		FIELD,
		ID,
		LUC
	}
	private static final long serialVersionUID = 5782230123197781710L;
	CorporateInquiryDTO corporateInfo;
	
	public TransactionReceiptListController(String dialogID,PG1111_CorporateDefinition page,ITable listView,CorporateInquiryDTO inquiryDTO,ParamModelDTO[] fieldList) throws Exception {
		super(page, listView,dialogID);
		this.corporateInfo=inquiryDTO;
		
		//Satır
		ListViewColumn column=new ListViewColumn(ListViewColumnType.Number,3,false,Fields.ROW.ordinal());
		addColumn(column);
		
		//Sütun
		column=new ListViewColumn(ListViewColumnType.Number,3,false,Fields.COLUMN.ordinal());
		addColumn(column);
		
		//Tip
		column=new ListViewColumn(ListViewColumnType.Combo,null,false,Fields.TYPE.ordinal());
		ComboboxDataDTO[] optionList=new ComboboxDataDTO[3];		
		optionList[0]=ListViewColumn.getComboBoxData("L", "Etiket");
		optionList[1]=ListViewColumn.getComboBoxData("V", "Değer");
		optionList[2]=ListViewColumn.getComboBoxData("E", "Açıklama");
		column.setComboBoxData(optionList);
		addColumn(column);
		
		//Etiket
		column=new ListViewColumn(ListViewColumnType.String,120,true,Fields.LABEL.ordinal());
		addColumn(column);
		
		//Alan
		column=new ListViewColumn(ListViewColumnType.Combo,null,true,Fields.FIELD.ordinal());
		if(fieldList!=null){
			optionList=new ComboboxDataDTO[fieldList.length];
			for (int i = 0; i < optionList.length; i++) {
				optionList[i]=ListViewColumn.getComboBoxData(fieldList[i].getText(), fieldList[i].getText());
			}
			column.setComboBoxData(optionList);
			addColumn(column);
		}
	}

	private Long string2Long(String val){
		if(val==null || StringUtils.hasNotText(val)) return null;
		else return Long.parseLong(val);
	}
	
	
	protected AutoTransactionReceiptModelDTO mapListViewRow2DTO(ITable currentList, int rowNum, String rowStatus) {
		AutoTransactionReceiptModelDTO modelDTO=new AutoTransactionReceiptModelDTO();
		modelDTO.setRow(Integer.parseInt(currentList.getCell(rowNum, Fields.ROW.ordinal())));
		modelDTO.setColumn(Integer.parseInt(currentList.getCell(rowNum, Fields.COLUMN.ordinal())));
		
		//Listview'dan gelen value'yu Key'e dönüştürür
		String selectedVal=getColumn(Fields.TYPE.ordinal()).getSelectedKey(currentList.getCell(rowNum, Fields.TYPE.ordinal()));
		modelDTO.setType(selectedVal);
		
		modelDTO.setLabel(currentList.getCell(rowNum, Fields.LABEL.ordinal()));
		
		selectedVal=getColumn(Fields.FIELD.ordinal()).getSelectedKey(currentList.getCell(rowNum, Fields.FIELD.ordinal()));
		modelDTO.setField(selectedVal);
		
		modelDTO.setProduct(corporateInfo.getProduct());
		modelDTO.setCorporate(corporateInfo.getCorporate());
		
		String id=currentList.getCell(rowNum, Fields.ID.ordinal());
		modelDTO.setId(string2Long(id));
		
		String luc=currentList.getCell(rowNum, Fields.LUC.ordinal());
		modelDTO.setLuc(string2Long(luc));
		
		return modelDTO;
	}

	
	protected String mapDto2ListViewRow(AutoTransactionReceiptModelDTO sourceDTO) {
		String row=StringUtils.suppressNull(sourceDTO.getRow()) + ITable.DELIMITER +
		StringUtils.suppressNull(sourceDTO.getColumn()) + ITable.DELIMITER +
				   getColumn(Fields.TYPE.ordinal()).getListViewComboDataString(sourceDTO.getType()) + ITable.DELIMITER +
				   StringUtils.suppressNull(sourceDTO.getLabel()) + ITable.DELIMITER +
				   getColumn(Fields.FIELD.ordinal()).getListViewComboDataString(sourceDTO.getField()) + ITable.DELIMITER +
				   StringUtils.suppressNull(sourceDTO.getId()) + ITable.DELIMITER +
				   StringUtils.suppressNull(sourceDTO.getLuc());
		return row;
	}

	
	protected AutoTransactionReceiptModelDTO getBlankRowDefaultValues() {
		AutoTransactionReceiptModelDTO dto=new AutoTransactionReceiptModelDTO();
		dto.setType("V");
		dto.setLuc(0L);
		return dto;
	}

	
	protected void deleteFromDatabase(AutoTransactionReceiptModelDTO dto) throws Exception {		
		RequestCorporateDebitSlipDeletion request=new RequestCorporateDebitSlipDeletion();
		request.setDebitSlipInfo(dto);
		JABSSupport.getJABS().getRemote(IFrmCorporateDefinitionFacade.class).deleteDebitSlip(request);
	}

	
	protected List<AutoTransactionReceiptModelDTO> listData() throws Exception {
		RequestListCorporateDetails request=new RequestListCorporateDetails();
		request.setCorporateInfo(corporateInfo);
		return JABSSupport.getJABS().getRemote(IFrmCorporateDefinitionFacade.class).listTransactionReceipts(request).getTransactionReceiptList();
	}
	
	private List<AutoTransactionReceiptModelDTO> array2List(AutoTransactionReceiptModelDTO[] array){
		if(array==null) return new ArrayList<AutoTransactionReceiptModelDTO>();
		else return Arrays.asList(array);
	}

	
	public RequestSaveCorporate fillRequest(RequestSaveCorporate request,EventData events, DisplayContext dc,ConversationContextManager cc) {
		request.setInsertedDebitSlipList(FWConversionUtil.arrayToList((AutoTransactionReceiptModelDTO[])getInsertedRows(events, dc, cc).toArray(new AutoTransactionReceiptModelDTO[0])));
		request.setUpdatedDebitSlipList(FWConversionUtil.arrayToList((AutoTransactionReceiptModelDTO[])getUpdatedRows(events, dc, cc).toArray(new AutoTransactionReceiptModelDTO[0])));
		return request;
	}

	
	public boolean fillForApproval(RequestSaveCorporate req, EventData events,DisplayContext dc, ConversationContextManager cc) throws Exception {
		AutoTransactionReceiptModelDTO[] insertedRecords= FWConversionUtil.listToArray(req.getInsertedDebitSlipList(),AutoTransactionReceiptModelDTO.class);
		AutoTransactionReceiptModelDTO[] updatedRecords= FWConversionUtil.listToArray( req.getUpdatedDebitSlipList(),AutoTransactionReceiptModelDTO.class);
		disablePageButtons(events, dc, cc);
		List<AutoTransactionReceiptModelDTO> allRecords=new LinkedList<AutoTransactionReceiptModelDTO>(array2List(insertedRecords));
		allRecords.addAll(array2List(updatedRecords));
		
		toApprovementMode(allRecords);

		return true;
	}
	
