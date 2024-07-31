package com.ykb.payments.bill.transaction.payment.mapper;

import com.ykb.payments.bill.common.enums.EnumCurrencyCode;
import com.ykb.payments.bill.common.enums.EnumYesNo;
import com.ykb.payments.bill.common.util.ChannelUtil;
import com.ykb.payments.bill.transaction.common.constant.BillTransactionConstant;
import com.ykb.payments.bill.transaction.external.harmoni.billpayment.rest.response.BalanceAccountPaymentInstrumentDTO;
import com.ykb.payments.bill.transaction.external.harmoni.billpayment.rest.response.CreditCardPaymentInstumentDTO;
import com.ykb.payments.bill.transaction.institution.enums.EnumPaymentMethod;
import com.ykb.payments.bill.transaction.payment.web.request.*;
import com.ykb.payments.bill.transaction.payment.web.response.*;
import com.ykb.payments.bill.transaction.subscriber.model.GetBillPaymentExpenseRequestDTO;
import com.ykb.payments.bill.transaction.subscriber.model.GetBillPaymentExpenseResponseDTO;
import org.apache.commons.lang3.StringUtils;
import org.mapstruct.*;
import org.springframework.util.CollectionUtils;

import java.time.ZoneId;
import java.util.ArrayList;
import java.util.Date;
import java.util.List;
import java.util.Optional;

@Mapper(componentModel = "spring")
public interface HarmoniMicroMapper {
	

	
	@Mapping(target = "agentCode", source = "requestSource.agentCode")
	@Mapping(target = "channelCode", source = "requestSource.channelCode", qualifiedByName = "harmoniToMicroChannel")
	@Mapping(target = "operatingBranchCode", source = "requestSource.operatingBranchCode", qualifiedByName = "extractOperatingBranchCode")
	@Mapping(target = "institutionCode", source = "institution")
	@Mapping(target = "productCode", source = "product")
	@Mapping(target = "subscriberNo", source = "subscriberNo1")
	@Mapping(target = "subscriberNoPartList", source = "request", qualifiedByName = "toSubscriberNoPartList")
	@Mapping(target = "identityNo", source = "request", qualifiedByName = "extractIdentityNo")
	public QueryBillsRequest toQueryBillRequest(RequestHarmoniQueryBills request);

	@Named("extractIdentityNo")
	public default Long extractIdentityNo(RequestHarmoniQueryBills request) {
		if (StringUtils.isNotBlank(request.getIdentityNo()) ) {
			return  Long.parseLong( request.getIdentityNo() );
		}
		return null;
	}
	@Mapping(target = "subscriberNo1", source = "queryBillsResponse.subscriberNoPartList", qualifiedByName = "toHarmoniSubscriberNo1")
	@Mapping(target = "subscriberNo2", source = "queryBillsResponse.subscriberNoPartList", qualifiedByName = "toHarmoniSubscriberNo2")
	@Mapping(target = "subscriberNo3", source = "queryBillsResponse.subscriberNoPartList", qualifiedByName = "toHarmoniSubscriberNo3")
	public ResponseHarmoniQueryBills toResponseHarmoniQueryBills(QueryBillsResponse queryBillsResponse, RequestHarmoniQueryBills harmoniRequest);

	@Mapping(target = "agentCode", source = "coreServiceBaseDataDTO.agentCode")
	@Mapping(target = "channelCode", source = "coreServiceBaseDataDTO.channelCode", qualifiedByName = "harmoniToMicroChannel")
	@Mapping(target = "operatingBranchCode", source = "coreServiceBaseDataDTO.operatingBranchCode", qualifiedByName = "extractOperatingBranchCode")
	@Mapping(target = "productCode", source = "product")
	@Mapping(target = "institutionCode", source = "institution")
	@Mapping(target = "billProvisionId", source = "provisionCode")
	@Mapping(target = "paymentMethodType", source = "paymentSourceCode", qualifiedByName = "toMicroPaymentSource")
	@Mapping(target = "accountPaymentMethodDetail", source = "request", qualifiedByName = "toAccountPaymentMethodDetail")
	@Mapping(target = "cashPaymentMethodDetail", source = "request", qualifiedByName = "toCashPaymentMethodDetail")
	@Mapping(target = "creditCardPaymentMethodDetail", source = "request", qualifiedByName = "toCartPaymentMethodDetail")
	@Mapping(target = "currency", source = "request", qualifiedByName = "extractCurrency")
	public DoBillPaymentRequest toDoBillPaymentRequest(RequestHarmoniDoBillPayment request);
	
	@Mapping(target = "contractNo", source = "contractNumber")
	public ResponseHarmoniDoBillPaymentResultDTO toResponseHarmoniDoBillPaymentResultDTO(
			DoBillPaymentResponse microResponse);
	
	@Mapping(target = "agentCode", source = "coreServiceBaseDataDTO.agentCode")
	@Mapping(target = "channelCode", source = "coreServiceBaseDataDTO.channelCode", qualifiedByName = "harmoniToMicroChannel")
	@Mapping(target = "operatingBranchCode", source = "coreServiceBaseDataDTO.operatingBranchCode", qualifiedByName = "extractOperatingBranchCode")
	@Mapping(target = "contractNumber", source = "contractNo")
	@Mapping(target = "institutionCode", source = "institution")
	@Mapping(target = "productCode", source = "product")
	public CancelBillPaymentRequest toCancelBillPaymentRequest(RequestHarmoniCancelBillPayment request);
	
	public ResponseHarmoniCancelBillPayment toResponseHarmoniCancelBillPayment(CancelBillPaymentResponse microResponse);
	
	@Mapping(target = "agentCode", source = "coreServiceBaseDataDTO.agentCode")
	@Mapping(target = "channelCode", source = "coreServiceBaseDataDTO.channelCode", qualifiedByName = "harmoniToMicroChannel")
	@Mapping(target = "operatingBranchCode", source = "coreServiceBaseDataDTO.operatingBranchCode")
	@Mapping(target = "paymentMethod", source = "paymentSource", qualifiedByName = "toMicroPaymentSource")
	@Mapping(target = "paymentAmount", source = "amount")
	@Mapping(target = "paymentCurrency", source = "currency")
	@Mapping(target = "cardNo", source = "creditCardNo")
	@Mapping(target = "accountNo", source = "creditCardNo")
	public GetBillPaymentExpenseRequestDTO toGetBillPaymentExpenseRequestDTO(
			RequestHarmoniGetBillPaymentExpense request);
	
	@Mapping(target = "expenseAmount", source = "commissionAmount")
	public ResponseHarmoniGetBillPaymentExpense toResponseHarmoniGetBillPaymentExpense(
			GetBillPaymentExpenseResponseDTO microResponse);
	
	@Named("extractCurrency")
	public default String extractCurrency(RequestHarmoniDoBillPayment request) {
		String currency = "";

		String microPaymentMethod = BillTransactionConstant.hmnMicroPaymentMap.get(request.getPaymentSourceCode());

		if (EnumPaymentMethod.CARD.getValue().equals(microPaymentMethod) || EnumPaymentMethod.PREPAIDCARD.getValue().equals(microPaymentMethod)) {

			currency = request.getCreditCardPaymentInstrument().getCurrencyCode();
		}

		else if (EnumPaymentMethod.ACCOUNT.getValue().equals(microPaymentMethod)) {

			currency = request.getBalanceAccountPaymentInstrument().getAccountCurrencyCode();
		}
		currency = StringUtils.isEmpty(currency) ? EnumCurrencyCode.TURKISH_LIRA_YTL.getValue() : currency;
		return currency;
	}
	
	@Named("toCartPaymentMethodDetail")
	public default CreditCardPaymentMethodDetailWebDTO toCartPaymentMethodDetail(RequestHarmoniDoBillPayment request) {
		CreditCardPaymentMethodDetailWebDTO paymentMethodDetail = null;

		String microPaymentMethod = BillTransactionConstant.hmnMicroPaymentMap.get(request.getPaymentSourceCode());

		if (EnumPaymentMethod.CARD.getValue().equals(microPaymentMethod) || EnumPaymentMethod.PREPAIDCARD.getValue().equals(microPaymentMethod)) {

			CreditCardPaymentInstumentDTO hmnPaymentMethodInstrument =request.getCreditCardPaymentInstrument();

			paymentMethodDetail = new CreditCardPaymentMethodDetailWebDTO();
			paymentMethodDetail.setCardNumber(hmnPaymentMethodInstrument.getCardNumber());
		}

		return paymentMethodDetail;
	}
	
	@Named("toCashPaymentMethodDetail")
	public default CashPaymentMethodDetailWebDTO toCashPaymentMethodDetail(RequestHarmoniDoBillPayment request) {
		CashPaymentMethodDetailWebDTO paymentMethodDetail = null;

		String microPaymentMethod = BillTransactionConstant.hmnMicroPaymentMap.get(request.getPaymentSourceCode());

		if (EnumPaymentMethod.CASH.getValue().equals(microPaymentMethod)) {

			/*CashPaymentInstrumentDTO hmnPaymentMethodInstrument = new ObjectMapper()
					.convertValue(request.getPaymentInstrument(), CashPaymentInstrumentDTO.class);
			paymentMethodDetail = new CashPaymentMethodDetailWebDTO();
			paymentMethodDetail.setDepositedAmount(hmnPaymentMethodInstrument.getDepositedAmount().toString());
			paymentMethodDetail.setDepositeAccountNumber(hmnPaymentMethodInstrument.getDepositeAccountNumber());
			paymentMethodDetail.setCoinPaybackAmount(hmnPaymentMethodInstrument.getCoinPaybackAmount());
			paymentMethodDetail.setCoinPaybackAccountNumber(hmnPaymentMethodInstrument.getCoinPaybackAccountNumber());
			paymentMethodDetail.setBanknotePaybackAmount(hmnPaymentMethodInstrument.getBanknotePaybackAmount());
			paymentMethodDetail.setBanknotePaybackAccountNumber(hmnPaymentMethodInstrument.getBanknotePaybackAccountNumber());*/
		}

		return paymentMethodDetail;
	}
		
	
	@Named("toAccountPaymentMethodDetail")
	public default AccountPaymentMethodDetailWebDTO toMicroPaymentSource(RequestHarmoniDoBillPayment request) {
		AccountPaymentMethodDetailWebDTO microAccountPaymentMethodDetail = null;

		String microPaymentMethod = BillTransactionConstant.hmnMicroPaymentMap.get(request.getPaymentSourceCode());

		if (EnumPaymentMethod.ACCOUNT.getValue().equals(microPaymentMethod)) {

			BalanceAccountPaymentInstrumentDTO hmnBalanceAccount = request.getBalanceAccountPaymentInstrument();
			microAccountPaymentMethodDetail = new AccountPaymentMethodDetailWebDTO();
			microAccountPaymentMethodDetail.setAccountBranchCode(hmnBalanceAccount.getAccountBranchCode());
			microAccountPaymentMethodDetail.setAccountNo(hmnBalanceAccount.getAccountNumber());

		}

		return microAccountPaymentMethodDetail;
	}
	
	
	@Named("toMicroPaymentSource")
	public default String toMicroPaymentSource(String hmnPaymentSource) {		
		return BillTransactionConstant.hmnMicroPaymentMap.get(hmnPaymentSource);
	}
	
	@Named("toHarmoniSubscriberNo1")
	public default String toHarmoniSubscriberNo1(List<SubsrciberNoPartResponseWebDTO> subscriberNoPartList) {
		return subscriberNoPartList!=null && subscriberNoPartList.size() == 1 ? subscriberNoPartList.get(0).getPartValue(): null;
	}
	
	@Named("toHarmoniSubscriberNo2")
	public default String toHarmoniSubscriberNo2(List<SubsrciberNoPartResponseWebDTO> subscriberNoPartList) {
		return subscriberNoPartList!=null && subscriberNoPartList.size() == 2 ? subscriberNoPartList.get(1).getPartValue(): null;
	}
	
	@Named("toHarmoniSubscriberNo3")
	public default String toHarmoniSubscriberNo3(List<SubsrciberNoPartResponseWebDTO> subscriberNoPartList) {
		return subscriberNoPartList!=null && subscriberNoPartList.size() == 3 ? subscriberNoPartList.get(2).getPartValue(): null;
	}
	
	
	@Named("harmoniToMicroChannel")
	public default String harmoniToMicroChannel(String harmoniChannel) {
		return ChannelUtil.convertChannel(harmoniChannel);
	}
	
	@Named("toSubscriberNoPartList")
	public default List<SubscriberNoPartRequestWebDTO> toSubscriberNoPartList(RequestHarmoniQueryBills request) {
		String subscriberNo1 = request.getSubscriberNo1();
		String subscriberNo2 = request.getSubscriberNo2();
		String subscriberNo3 = request.getSubscriberNo3();

		List<SubscriberNoPartRequestWebDTO> subscriberNoPartList = new ArrayList<>();

		if (StringUtils.isNotEmpty(subscriberNo1)) {
			SubscriberNoPartRequestWebDTO part = new SubscriberNoPartRequestWebDTO();
			part.setPartNo(1);
			part.setPartValue(subscriberNo1);

			subscriberNoPartList.add(part);
		}

		if (StringUtils.isNotEmpty(subscriberNo2)) {
			SubscriberNoPartRequestWebDTO part = new SubscriberNoPartRequestWebDTO();
			part.setPartNo(2);
			part.setPartValue(subscriberNo2);

			subscriberNoPartList.add(part);
		}

		if (StringUtils.isNotEmpty(subscriberNo3)) {
			SubscriberNoPartRequestWebDTO part = new SubscriberNoPartRequestWebDTO();
			part.setPartNo(3);
			part.setPartValue(subscriberNo3);

			subscriberNoPartList.add(part);
		}

		return subscriberNoPartList;
	}
	
	@AfterMapping
	default void afterToGetQueryBillsResponse(@MappingTarget final ResponseHarmoniQueryBills harmoniResponse,
			RequestHarmoniQueryBills harmoniRequest, QueryBillsResponse microResponse) {

		List<HmnBaseBillDTO> harmoniBilllist = microResponse.getBillList().stream().map(microBill -> {
			HmnBaseBillDTO hmn = new HmnBaseBillDTO();
			hmn.setProduct(microResponse.getProductCode());
			hmn.setInstitution(microResponse.getInstitutionCode());
			hmn.setSubscriberNo(microResponse.getSubscriberNo());
			hmn.setSubscriberName(microResponse.getSubscriberName());
			hmn.setPayable(microBill.isPayable() ? EnumYesNo.YES.getValue() : EnumYesNo.NO.getValue());
			if (!CollectionUtils.isEmpty(microResponse.getSubscriberNoPartList())) {
				Optional<SubsrciberNoPartResponseWebDTO> firstSubscriberNo = microResponse.getSubscriberNoPartList()
						.stream().filter(f -> f.getPartNo().equals("1")).findAny();
				
				Optional<SubsrciberNoPartResponseWebDTO> secondSubscriberNo = microResponse.getSubscriberNoPartList()
						.stream().filter(f -> f.getPartNo().equals("2")).findAny();
				
				Optional<SubsrciberNoPartResponseWebDTO> thirtSubscriberNo = microResponse.getSubscriberNoPartList()
						.stream().filter(f -> f.getPartNo().equals("3")).findAny();

				hmn.setSubscriberNoPart1(firstSubscriberNo.isPresent() ? firstSubscriberNo.get().getPartValue() : null);
				hmn.setSubscriberNoPart2(
						firstSubscriberNo.isPresent() ? secondSubscriberNo.get().getPartValue() : null);
				hmn.setSubscriberNoPart3(thirtSubscriberNo.isPresent() ? thirtSubscriberNo.get().getPartValue() : null);
			}
			hmn.setBillDueDate(Date.from(microBill.getBillDueDate().atStartOfDay(ZoneId.systemDefault()).toInstant()));
			hmn.setBillNo(microBill.getBillNo());
			hmn.setBillTerm(microBill.getBillTerm());
			hmn.setBillAmount(microBill.getBillAmount());
			//TODO currency control belki ??
			hmn.setCurrency(microBill.getCurrency());
			hmn.setBankReferenceNo(microBill.getBillProvisionId().toString());

			
			return hmn;
		}).toList();

		harmoniResponse.setBillList(harmoniBilllist);
	}

	@Named("extractOperatingBranchCode")
	 default String extractOperatingBranchCode(String operatingBranchCode) {
		return StringUtils.isEmpty(operatingBranchCode) ? BillTransactionConstant.GENERAL_BRANCH_CODE : operatingBranchCode;
	}


}
