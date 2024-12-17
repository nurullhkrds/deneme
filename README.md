[EXCEPTION SOURCE INFORMATION]
	File: SQLStateConverter.java
	Class: org.hibernate.exception.SQLStateConverter
	Method: convert
	Line Number: 102

[EXCEPTION STACK TRACE]
com.ykb.hmn.inf.core.exception.internal.HmnException:  ( 10002 ) Data Access Exception occurred [ORA-01858: a non-numeric character was found where a numeric was expected
could not execute query]
	at org.hibernate.exception.SQLStateConverter.convert(SQLStateConverter.java:102)
	at org.hibernate.exception.JDBCExceptionHelper.convert(JDBCExceptionHelper.java:66)
	at org.hibernate.loader.Loader.doList(Loader.java:2541)
	at org.hibernate.loader.Loader.listIgnoreQueryCache(Loader.java:2281)
	at org.hibernate.loader.Loader.list(Loader.java:2276)
	at org.hibernate.loader.custom.CustomLoader.list(CustomLoader.java:316)
	at org.hibernate.impl.SessionImpl.listCustomQuery(SessionImpl.java:1842)
	at org.hibernate.impl.AbstractSessionImpl.list(AbstractSessionImpl.java:165)
	at org.hibernate.impl.SQLQueryImpl.list(SQLQueryImpl.java:157)
	at com.ykb.hmn.inf.core.qe.QueryExecutor.innerList(QueryExecutor.java:185)
	at com.ykb.hmn.inf.core.qe.QueryExecutor.listWithDynamicQuery(QueryExecutor.java:439)
	at com.ykb.hmn.inf.core.qe.QueryExecutor.listWithDynamicQuery(QueryExecutor.java:497)
	at com.ykb.hmn.mig.common.transformer.QuerySupport.dynamicQuery(QuerySupport.java:51)
	at com.ykb.hmn.pym.billpayments.service.BillPaymentDataService.getPaymentLogsByParameters(BillPaymentDataService.java:2230)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at org.springframework.aop.support.AopUtils.invokeJoinpointUsingReflection(AopUtils.java:333)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.invokeJoinpoint(ReflectiveMethodInvocation.java:190)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:157)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:173)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:173)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:173)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:173)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:173)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:173)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:173)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:173)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:173)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:173)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:173)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:173)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:173)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:173)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:173)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:173)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:173)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:173)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:173)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:173)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:173)
	at org.springframework.transaction.interceptor.TransactionInterceptor$1.proceedWithInvocation(TransactionInterceptor.java:99)
	at org.springframework.transaction.interceptor.TransactionAspectSupport.invokeWithinTransaction(TransactionAspectSupport.java:282)
	at org.springframework.transaction.interceptor.TransactionInterceptor.invoke(TransactionInterceptor.java:96)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:168)
	at org.springframework.aop.interceptor.ExposeInvocationInterceptor.invoke(ExposeInvocationInterceptor.java:92)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:179)
	at org.springframework.aop.aspectj.MethodInvocationProceedingJoinPoint.proceed(MethodInvocationProceedingJoinPoint.java:85)
	at com.ykb.hmn.inf.core.service.aspect.profiler.ServiceProfiler.profile(ServiceProfiler.java:282)
	at sun.reflect.GeneratedMethodAccessor201.invoke(Unknown Source)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)
	at org.springframework.aop.aspectj.AbstractAspectJAdvice.invokeAdviceMethodWithGivenArgs(AbstractAspectJAdvice.java:629)
	at org.springframework.aop.aspectj.AbstractAspectJAdvice.invokeAdviceMethod(AbstractAspectJAdvice.java:618)
	at org.springframework.aop.aspectj.AspectJAroundAdvice.invoke(AspectJAroundAdvice.java:70)
	at org.springframework.aop.framework.ReflectiveMethodInvocation.proceed(ReflectiveMethodInvocation.java:168)
	at org.springframework.aop.framework.JdkDynamicAopProxy.invoke(JdkDynamicAopProxy.java:213)
	at com.sun.proxy.$Proxy229.getPaymentLogsByParameters(Unknown Source)
	at com.ykb.hmn.pym.billpayments.facade.BillPaymentDataFacade.getPaymentLogsByParameters(BillPaymentDataFacade.java:1201)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:498)



public class GetPaymentLogsTransformer implements DynamicQueryTransformer<PaidBillLogDTO> {
	
	String institution;
	
	String product;
	
	List<String> subscriberList;
	
	Date startDate;
	
	Date endDate; 
	
	Date paymentDate; 
	
	String billNo; 
	
	List<String> paymentTypeList;
	
	

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

		    map.put(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.IN_END_DATE, endDate);
		    map.put(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.IN_START_DATE, startDate);
		    map.put(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.IN_INSTITUTION, institution);
		    map.put(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.IN_PRODUCT, product);
		    map.put(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.IN_ODMTIP_LIST, paymentTypeList);

		    if(StringUtils.hasText(billNo)){
			    map.put(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.IN_BILL_NO, billNo);
				flagList.add(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.IN_BILL_NO);
		    }
		    
		    if(paymentDate != null){
			    map.put(NQConsts.GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL.IN_ODMTARIH, paymentDate);
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
