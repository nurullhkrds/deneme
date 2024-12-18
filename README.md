public class GetPaymentsTransformer implements DynamicQueryTransformer<PaidBillDTO> {
	
	String institution;
	
	String product;
	
	String []  subscriberList;
	
	Date paymentDate; 
	
	String billNo; 
	
	String [] paymentTypeList;
	
    SimpleDateFormat dateFormat = new SimpleDateFormat("dd.MM.yyyy");

  

	public GetPaymentsTransformer(String institution, String product, String []  subscriberList,
			Date paymentDate, String billNo, String []  paymentTypeList) {
		super();
		this.institution = institution;
		this.product = product;
		this.subscriberList = subscriberList;
		this.paymentDate = paymentDate;
		this.billNo = billNo;
		this.paymentTypeList = paymentTypeList;
	}

	@Override
	public PaidBillDTO convert(Map<String, Object> map) throws HmnServiceException {
		PaidBillDTO dto= new PaidBillDTO();
		
	
		dto.setInstitution(ObjectUtils.objToTargetType(map.get(NQConsts.GET_T_OTO_FATURA_FOR_LOG.OUT_INSITUTION), String.class));
		dto.setProduct(ObjectUtils.objToTargetType(map.get(NQConsts.GET_T_OTO_FATURA_FOR_LOG.OUT_PRODUCT), String.class));
		dto.setBillNo(ObjectUtils.objToTargetType(map.get(NQConsts.GET_T_OTO_FATURA_FOR_LOG.OUT_BILL_NO), String.class));
		dto.setSubscriberNo(ObjectUtils.objToTargetType(map.get(NQConsts.GET_T_OTO_FATURA_FOR_LOG.OUT_SUBSCRIBER_NO), String.class));
		dto.setTotalAmount(ObjectUtils.objToTargetType(map.get(NQConsts.GET_T_OTO_FATURA_FOR_LOG.OUT_TOTAL_AMOUNT), BigDecimal.class));
		dto.setPaidAmount(ObjectUtils.objToTargetType(map.get(NQConsts.GET_T_OTO_FATURA_FOR_LOG.OUT_PAID_AMOUNT), BigDecimal.class));
		dto.setDueDate(ObjectUtils.objToTargetType(map.get(NQConsts.GET_T_OTO_FATURA_FOR_LOG.OUT_DUE_DATE), String.class));
		dto.setPaymentDate(ObjectUtils.objToTargetType(map.get(NQConsts.GET_T_OTO_FATURA_FOR_LOG.OUT_PAYMENT_DATE), String.class));
		dto.setPaymentType(ObjectUtils.objToTargetType(map.get(NQConsts.GET_T_OTO_FATURA_FOR_LOG.OUT_PAYMENT_TYPE), String.class));
		dto.setReferenceNo(ObjectUtils.objToTargetType(map.get(NQConsts.GET_T_OTO_FATURA_FOR_LOG.OUT_REFERENCE_NO), String.class));

		return dto;
	}

	@Override
	public DynamicQueryTransformerDTO prepare() {
		
		 	Map<String, Object> map = new HashMap<String, Object>();
			List<String> flagList = new ArrayList<String>();

		        map.put(NQConsts.GET_T_OTO_FATURA_FOR_LOG.IN_INSTITUTION, institution);
		        map.put(NQConsts.GET_T_OTO_FATURA_FOR_LOG.IN_PRODUCT, product);
		        map.put(NQConsts.GET_T_OTO_FATURA_FOR_LOG.IN_ODMTIP_LIST, paymentTypeList);

		        
		    if(StringUtils.hasText(billNo)){
			    map.put(NQConsts.GET_T_OTO_FATURA_FOR_LOG.IN_BILL_NO, billNo);
				flagList.add(NQConsts.GET_T_OTO_FATURA_FOR_LOG.IN_BILL_NO);
		    }
		    
		    if(paymentDate != null){
			    map.put(NQConsts.GET_T_OTO_FATURA_FOR_LOG.IN_ODMTARIH, dateFormat.format(paymentDate));
				flagList.add(NQConsts.GET_T_OTO_FATURA_FOR_LOG.IN_ODMTARIH);

		    }
		   
		    if(subscriberList !=null && subscriberList.length > 0){
			    map.put(NQConsts.GET_T_OTO_FATURA_FOR_LOG.IN_SUBSCRIBER_LIST, subscriberList);
				flagList.add(NQConsts.GET_T_OTO_FATURA_FOR_LOG.IN_SUBSCRIBER_LIST);

		    }
		    
			DynamicQueryTransformerDTO dynamicQueryTransformerDto = new DynamicQueryTransformerDTO();

			dynamicQueryTransformerDto.setMap(map);
			dynamicQueryTransformerDto.setFlagList(flagList);
			
			return dynamicQueryTransformerDto;
	}

}
