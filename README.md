@Mapper(componentModel = "spring")
public interface MicroHarmoniMapper {

    @Mapping(target = "product", source = "productCode")
    @Mapping(target = "institution", source = "institutionCode")
    @Mapping(target = "currencyCode",  source = "request", qualifiedByName = "extractCurrencyCode")
    @Mapping(target = "subscriberNo1", source = "request.subscriberNoPartList", qualifiedByName = "toHarmoniSubscriberNo1")
    @Mapping(target = "subscriberNo2", source = "request.subscriberNoPartList", qualifiedByName = "toHarmoniSubscriberNo2")
    @Mapping(target = "subscriberNo3", source = "request.subscriberNoPartList", qualifiedByName = "toHarmoniSubscriberNo3")
    @Mapping(target = "channelCode", source = "request.channelCode", qualifiedByName = "harmoniToMicroChannel")
    RequestQueryBillsHmn toRequestQueryBillsHmn(QueryBillsRequest request);

    @Named("extractCurrencyCode")
    default String extractCurrencyCode(QueryBillsRequest request) {
        return StringUtils.isNotBlank(request.getCurrency()) && request.getCurrency().equals(EnumCurrencyCode.TURKISH_LIRA.getValue())
                ? EnumCurrencyCode.TURKISH_LIRA_YTL.getValue() : request.getCurrency();
    }

    @Named("toHarmoniSubscriberNo1")
    default String toHarmoniSubscriberNo1(List<SubscriberNoPartRequestWebDTO> subscriberNoPartList) {
        return subscriberNoPartList!=null && subscriberNoPartList.size() == 1 ? subscriberNoPartList.get(0).getPartValue(): null;
    }

    @Named("toHarmoniSubscriberNo2")
    default String toHarmoniSubscriberNo2(List<SubscriberNoPartRequestWebDTO> subscriberNoPartList) {
        return subscriberNoPartList!=null && subscriberNoPartList.size() == 2 ? subscriberNoPartList.get(1).getPartValue(): null;
    }

    @Named("toHarmoniSubscriberNo3")
    default String toHarmoniSubscriberNo3(List<SubscriberNoPartRequestWebDTO> subscriberNoPartList) {
        return subscriberNoPartList!=null && subscriberNoPartList.size() == 3 ? subscriberNoPartList.get(2).getPartValue(): null;
    }

    @Named("harmoniToMicroChannel")
    default String harmoniToMicroChannel(String channel) {
        return ChannelUtil.convertToHarmoniChannel(channel);
    }

    @Mapping(target = "productCode",source = "queryBillsRequest.productCode")
    @Mapping(target = "institutionCode",source = "queryBillsRequest.institutionCode")
    @Mapping(target = "debtTypeID",source = "queryBillsRequest.debtTypeID")
    @Mapping(target = "subscriberNo",source = "responseQueryBillsMicro", qualifiedByName = "extractSubscriberNo")
    @Mapping(target = "subscriberName",source = "responseQueryBillsMicro", qualifiedByName = "extractSubscriberName")
    @Mapping(target = "billList",source = "responseQueryBillsMicro", qualifiedByName = "extractBillList")
    QueryBillsResponse toQueryBillsResponse(ResponseQueryBillsHmn responseQueryBillsMicro,QueryBillsRequest queryBillsRequest);

    @Named("extractSubscriberNo")
    default String extractSubscriberNo(ResponseQueryBillsHmn responseQueryBillsMicro) {
        return  responseQueryBillsMicro.getBillList()!= null && !responseQueryBillsMicro.getBillList().isEmpty() ?  responseQueryBillsMicro.getBillList().get(0).getSubscriberNo() : null;
    }
    @Named("extractSubscriberName")
    default String extractSubscriberName(ResponseQueryBillsHmn responseQueryBillsMicro) {
        return  responseQueryBillsMicro.getBillList()!= null && !responseQueryBillsMicro.getBillList().isEmpty() ?  responseQueryBillsMicro.getBillList().get(0).getSubscriberName() : null;
    }
    @Named("extractBillList")
    default List<QueriedBillResponseWebDTO>  extractBillList(ResponseQueryBillsHmn responseQueryBillsMicro) {
        List<QueriedBillResponseWebDTO> billList = new ArrayList<>();
        QueriedBillResponseWebDTO queriedBillResponseWebDTO;
        for (HmnQueriedBills hmnQueriedBills : responseQueryBillsMicro.getBillList()) {
            queriedBillResponseWebDTO = new QueriedBillResponseWebDTO();
            queriedBillResponseWebDTO.setBillNo(hmnQueriedBills.getBillNo());
            queriedBillResponseWebDTO.setBillTerm(hmnQueriedBills.getBillTerm());
            queriedBillResponseWebDTO.setBillAmount(hmnQueriedBills.getBillAmount());
            queriedBillResponseWebDTO.setBillProvisionId(hmnQueriedBills.getBankReferenceNo());
            queriedBillResponseWebDTO.setPayable(StringUtils.isEmpty(hmnQueriedBills.getPayable()) || hmnQueriedBills.getPayable().equals(EnumYesNo.YES.getValue()) ?  true : false);
            queriedBillResponseWebDTO.setBillDueDate(DateUtils.convertDateTOLocalDate(hmnQueriedBills.getBillDueDate()));
            queriedBillResponseWebDTO.setCurrency(hmnQueriedBills.getCurrency());
            billList.add(queriedBillResponseWebDTO);
        }
        return billList;
    }


    RequestBillPaymentExpenseHmn toRequestBillPaymentExpenseHmn(GetBillPaymentExpenseRequestDTO getBillPaymentExpenseRequestDTO);

    GetBillPaymentExpenseResponseDTO toGetBillPaymentExpenseResponseDTO(ResponseBillPaymentExpenseHmn responseBillPaymentExpenseHmn);

    @Mapping(target = "channelCode", source = "request.channelCode", qualifiedByName = "harmoniToMicroChannel")
    RequestPayBillHmn toRRequestPayBillHmn(DoBillPaymentRequest request);

    DoBillPaymentResponse toResponsePayBillHmn(ResponsePayBillHmn responsePayBillHmn);
}
