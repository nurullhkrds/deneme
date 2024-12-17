public class GetPaymentLogsTransformer implements DynamicQueryTransformer<PaidBillLogDTO> {
    
    String institution;
    String product;
    List<String> subscriberList;
    Date startDate;
    Date endDate; 
    Date paymentDate; 
    String billNo; 
    List<String> paymentTypeList;
    
    SimpleDateFormat dateFormat = new SimpleDateFormat("yyyy-MM-dd");

    public GetPaymentLogsTransformer(String institution, String product, Date startDate,
            Date endDate, List<String> paymentTypeList) {
        super();
        this.institution = institution;
        this.product = product;
        this.startDate = startDate;
        this.endDate = endDate;
        this.paymentTypeList = paymentTypeList;
    }

    public GetPaymentLogsTransformer(String institution, String product, List<String> subscriberList, Date startDate,
            Date endDate, Date paymentDate, String billNo, List<String> paymentTypeList) {
        super();
        this.institution = institution;
        this.product = product;
        this.subscriberList = subscriberList;
        this.startDate = startDate;
        this.endDate = endDate;
        this.paymentDate = paymentDate;
        this.billNo = billNo;
        this.paymentTypeList = paymentTypeList;
    }

    @Override
    public PaidBillLogDTO convert(Map<String, Object> map) throws HmnServiceException {
        PaidBillLogDTO dto= new PaidBillLogDTO();
        
        dto.setInstitution(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_INSITUTION), String.class));
        dto.setProduct(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_PRODUCT), String.class));
        dto.setBillNo(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_BILL_NO), String.class));
        dto.setLogRecordNo(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_LOG_RECORD_NO), String.class));
        dto.setLogDate(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_LOG_DATE), String.class));
        dto.setSubscriberNo(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_SUBSCRIBER_NO), String.class));
        dto.setInstitutionReturnCode(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_INSTITUTION_RETURN_CODE), String.class));
        dto.setInstitutionReturnText(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_INSTITUTION_RETURN_TEXT), String.class));
        dto.setTotalAmount(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_TOTAL_AMOUNT), BigDecimal.class));
        dto.setPaidAmount(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_PAID_AMOUNT), BigDecimal.class));
        dto.setDueDate(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_DUE_DATE), Date.class));
        dto.setPaymentDate(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_PAYMENT_DATE), Date.class));
        dto.setPaymentType(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_PAYMENT_TYPE), String.class));
        dto.setReferenceNo(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_REFERENCE_NO), String.class));

        return dto;
    }

    @Override
    public DynamicQueryTransformerDTO prepare() {
        Map<String, Object> map = new HashMap<String, Object>();
        List<String> flagList = new ArrayList<String>();

        map.put(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.IN_END_DATE, dateFormat.format(endDate));
        map.put(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.IN_START_DATE, dateFormat.format(startDate));
        map.put(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.IN_INSTITUTION, institution);
        map.put(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.IN_PRODUCT, product);
        map.put(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.IN_ODMTIP_LIST, paymentTypeList);

        if(StringUtils.hasText(billNo)){
            map.put(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.IN_BILL_NO, billNo);
            flagList.add(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.IN_BILL_NO);
        }

        if(paymentDate != null){
            map.put(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.IN_ODMTARIH, dateFormat.format(paymentDate));
            flagList.add(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.IN_ODMTARIH);
        }

        if(subscriberList !=null && subscriberList.size() > 0){
            map.put(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.IN_SUBSCRIBER_LIST, subscriberList);
            flagList.add(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.IN_SUBSCRIBER_LIST);
        }

        DynamicQueryTransformerDTO dynamicQueryTransformerDto = new DynamicQueryTransformerDTO();

        dynamicQueryTransformerDto.setMap(map);
        dynamicQueryTransformerDto.setFlagList(flagList);

        return dynamicQueryTransformerDto;
    }
}
