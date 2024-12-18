package com.ykb.hmn.pym.billpayments.transformer;

import java.math.BigDecimal;
import java.text.SimpleDateFormat;
import java.util.ArrayList;
import java.util.Date;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import com.ykb.hmn.mig.common.transformer.DynamicQueryTransformer;
import com.ykb.hmn.mig.common.transformer.DynamicQueryTransformerDTO;
import com.ykb.harmoni.inf.core.service.intf.HmnServiceException;
import com.ykb.hmn.mig.common.util.ObjectUtils;
import com.ykb.hmn.mig.common.util.StringUtils;
import com.ykb.hmn.pym.billpayments.constant.NQConsts;
import com.ykb.hmn.pym.billpayments.dto.data.PaidBillLogDTO;
import com.ykb.hmn.pym.billpayments.util.PYMErrorUtils;

public class GetPaymentLogsTransformer implements DynamicQueryTransformer<PaidBillLogDTO> {
	
	String institution;
	
	String product;
	
	String []  subscriberList;
	
	Date startDate;
	
	Date endDate; 
	
	Date paymentDate; 
	
	String billNo; 
	
	String [] paymentTypeList;
	
    SimpleDateFormat dateFormat = new SimpleDateFormat("dd.MM.yyyy");
	 SimpleDateFormat logDateFormat = new SimpleDateFormat("dd.MM.yy HH:mm:ss");
     SimpleDateFormat outputDateFormat = new SimpleDateFormat("dd.MM.yyyy");

    

	public GetPaymentLogsTransformer(String institution, String product, Date startDate,
			Date endDate, String []  paymentTypeList) {
		super();
		this.institution = institution;
		this.product = product;
		this.startDate = startDate;
		this.endDate = endDate;
		this.paymentTypeList = paymentTypeList;
	}


	public GetPaymentLogsTransformer(String institution, String product, String []  subscriberList, Date startDate,
			Date endDate, Date paymentDate, String billNo, String []  paymentTypeList) {
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
		dto.setSubscriberNo(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_SUBSCRIBER_NO), String.class));
		dto.setInstitutionReturnCode(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_INSTITUTION_RETURN_CODE), String.class));
		dto.setInstitutionReturnText(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_INSTITUTION_RETURN_TEXT), String.class));
		dto.setTotalAmount(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_TOTAL_AMOUNT), BigDecimal.class));
		dto.setPaidAmount(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_PAID_AMOUNT), BigDecimal.class));
		dto.setPaymentType(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_PAYMENT_TYPE), String.class));
		dto.setReferenceNo(ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_REFERENCE_NO), String.class));

		 try {
	            // dueDate processing
	            String dueDateString = ObjectUtils.objToTargetType(map.get(NQConsts.GET_T_OTO_FATURA_FOR_LOG.OUT_DUE_DATE), String.class);
	            Date dueDate = dateFormat.parse(dueDateString);
	            dto.setDueDate(outputDateFormat.format(dueDate));

	            // paymentDate processing
	            String paymentDateString = ObjectUtils.objToTargetType(map.get(NQConsts.GET_T_OTO_FATURA_FOR_LOG.OUT_PAYMENT_DATE), String.class);
	            Date paymentDate = dateFormat.parse(paymentDateString);
	            dto.setPaymentDate(outputDateFormat.format(paymentDate));

	            // logDate processing
	            String logDateString = ObjectUtils.objToTargetType(map.get(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.OUT_LOG_DATE), String.class);
	            Date logDate = logDateFormat.parse(logDateString);
	            dto.setLogDate(logDateFormat.format(logDate));

	        } catch (Exception e) {
	            PYMErrorUtils.throwGenericErrorServiceResult( "getPaymentLogsByParameters", e.getClass().getSimpleName(), "GetPaymentLogsTransformer", "date" );     
	        }
		
	
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
		   
		    if(subscriberList !=null && subscriberList.length > 0){
			    map.put(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.IN_SUBSCRIBER_LIST, subscriberList);
				flagList.add(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.IN_SUBSCRIBER_LIST);

		    }
		    
			DynamicQueryTransformerDTO dynamicQueryTransformerDto = new DynamicQueryTransformerDTO();

			dynamicQueryTransformerDto.setMap(map);
			dynamicQueryTransformerDto.setFlagList(flagList);
			
			return dynamicQueryTransformerDto;
	}

}
