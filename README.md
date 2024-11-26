@SuppressWarnings({ "rawtypes" })
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




public TransactionReceiptListController(String dialogID,PG1111_CorporateDefinition page,ITable listView,CorporateInquiryDTO inquiryDTO,ParamModelDTO[] fieldList) throws Exception {
		super(page, listView,dialogID);
		this.corporateInfo=inquiryDTO;
}


	protected List<AutoTransactionReceiptModelDTO> listData() throws Exception {
		RequestListCorporateDetails request=new RequestListCorporateDetails();
		request.setCorporateInfo(corporateInfo);
		return JABSSupport.getJABS().getRemote(IFrmCorporateDefinitionFacade.class).listTransactionReceipts(request).getTransactionReceiptList();
	}


BU ŞEKİLDE DBDEN VERİYİ ÇEKMİŞ ÖRNEK ...


