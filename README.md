   public static interface GET_PRODUCT_DETAIL_LIST_BY_CIF_NO_AND_PRODUCT{
    	public static final String QUERY_NAME = "GET_PRODUCT_DETAIL_LIST_BY_CIF_NO_AND_PRODUCT";
    	
    	public static final String IN_CIF_NO= "CIF_NO";
    	public static final String IN_PRODUCT="PRODUCT";
    	
    	public static final String OUT_PRODUCT = "PRODUCT";
    	public static final String OUT_INSITUTION = "INSTITUTION";
    	public static final String OUT_SUBSCRIBER_NO = "SUBSCRIBER_NO";
    	public static final String OUT_ENTRY_DATE = "ENTRY_DATE";
    	public static final String OUT_REVERSIBLE= "REVERSIBLE";

    	
    }

	@Override
	public CoreServiceResultDTO<List<ProductDetailDTO>> getProductDetailListByCifNoAndProduct(String cifno,
			String product) throws HmnServiceException {
		CoreServiceResultDTO<List<ProductDetailDTO>> response = new CoreServiceResultDTO<List<ProductDetailDTO>>();

		List<ProductDetailDTO> resultList= QuerySupport.query(NQConsts.GET_PRODUCT_DETAIL_LIST_BY_CIF_NO_AND_PRODUCT.QUERY_NAME,new GetProductDetailTransformer(cifno,product));
		if (resultList == null || resultList.isEmpty()) {
			response.setStatus(BillPaymentsConsts.RESPONSE_STATUS.ERROR);
			response.setResponseMessage(PYMResponseUtils.createResponseStatusDTO(EnumResponseCodes.GENERIC__NO_RECORD_FOUND_ERROR.getValue(), null));
		}

		else {
			response.setStatus(BillPaymentsConsts.RESPONSE_STATUS.SUCCESS);
			response.setResponseMessage(PYMResponseUtils.createResponseStatusDTO(EnumResponseCodes.SUCCESS.getValue(), null));
			response.setResult(resultList);
		}
		return response;
	}

public class GetProductDetailTransformer implements QueryTransformer<ProductDetailDTO> {
	
	private String cifno;
	
	private String product;
	
	
	public GetProductDetailTransformer(String cifno, String product) {
		super();
		this.cifno=cifno;
		this.product=product;
		
	}
	

	@Override
	public ProductDetailDTO convert(Map<String, Object> map) throws HmnServiceException {
		ProductDetailDTO dto = new ProductDetailDTO();
		dto.setProduct(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PRODUCT_DETAIL_LIST_BY_CIF_NO_AND_PRODUCT.OUT_PRODUCT), String.class));
		dto.setInstitution(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PRODUCT_DETAIL_LIST_BY_CIF_NO_AND_PRODUCT.OUT_INSITUTION), String.class));
		dto.setSubscriberNo(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PRODUCT_DETAIL_LIST_BY_CIF_NO_AND_PRODUCT.OUT_SUBSCRIBER_NO), String.class));
		dto.setEntryDate(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PRODUCT_DETAIL_LIST_BY_CIF_NO_AND_PRODUCT.OUT_ENTRY_DATE), Date.class));
		dto.setReversible(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PRODUCT_DETAIL_LIST_BY_CIF_NO_AND_PRODUCT.OUT_REVERSIBLE), Boolean.class));

		return dto;
	}

	@Override
	public Map<String, Object> prepare() {
	    Map<String, Object> map = new HashMap<String, Object>();
	    map.put(NQConsts.GET_PRODUCT_DETAIL_LIST_BY_CIF_NO_AND_PRODUCT.IN_CIF_NO, cifno);
	    map.put(NQConsts.GET_PRODUCT_DETAIL_LIST_BY_CIF_NO_AND_PRODUCT.IN_PRODUCT, product);
		return map;	
		}

}


 ( 10025 ) Bad Sql Grammar Exception occurred [ORA-00933: SQL command not properly ended
could not execute query]
	at org.hibernate.exception.SQLStateConverter.convert(SQLStateConverter.java:92)
	at org.hibernate.exception.JDBCExceptionHelper.convert(JDBCExceptionHelper.java:66)
	at org.hibernate.loader.Loader.doList(Loader.java:2541)
	at org.hibernate.loader.Loader.listIgnoreQueryCache(Loader.java:2281)
	at org.hibernate.loader.Loader.list(Loader.java:2276)
	at org.hibernate.loader.custom.CustomLoader.list(CustomLoader.java:316)
