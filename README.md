CEPTEL,ELEKTRİK,TELEKOM,SU,DOĞALGAZ,TAHSİLAT,KREDİLER
Müşterinin talimat durumuna ve talimatının ürün grubuna göre aşağıdaki maddeler şeklinde ilerlenecektir.

Müşterinin aktif talimatı hiç yok ise: Müşteriye aktif talimatının bulunmadığı bilgisi verilmelidir. 
Müşterinin aktif talimatı var ve yukarıdaki tabloda işaretlenen ürünlerden biri ise: Müşteriye aktif talimatları belirlenen şekilde dönülmelidir.
Müşterinin talimatı belirlenen ürün grupları dışında ise ( örn spt):  Müşteriye aktif talimatının iptalinin IVR kanalı üzerinden yapılamayacağı bilgisi verilmelidir. 
Kapsam:

Talimat sorgulama servisi için; ürün grubu bilgisini ilk olarak müşteri mi belirleyecek? Örneğin, müşterinin CEPTEL, DOĞALGAZ, SU ve ELEKTRİK ürünlerinden talimatı olsun. Hangi ürün grubu üzerinde işlem yapacağını müşteri belirledikten sonra ürün grubuna göre mi ilk 8 talimatı size dönülmeli? AY: Evet, müşteriye biz hangi ürün tipinde faturası var ise o seçenekleri sunuyoruz, daha sonra müşterinin seçtiği ürün tipine göre bu tipe uygun ilk 8 fatura halihazırda sizden dönmüş oluyor bu bilgileri sunuyoruz. Örneğin, müşterinin 5 su + 4 elektrik + 3 doğalgaz faturası varsa, doğalgaz için 1’i, elektrik için 2’yi, su faturanızla işlem yapmak için 3’ü tuşlayınız. Örneğin 2 tuşladı ben müşteriye 4 adet talimat sırayla sunulur. (Aslında akışın başında ben sizin servisinizi cif ile tetiklediğimde müşterinin tüm talimatları gelecek ben buradan kaç farklı ürün tipi varsa onları müşteriye listeleyeceğim, müşterinin seçtiği ürün tipine göre de sizin gönderdiğiniz faturaları listeleyeceğim.


Talimat sorgulama servisi için; müşterinin birden fazla ürün grubunda talimatı varsa DİĞER adı altında ürün grubu sizin tarafta oluşturulacak mıdır? Oluşturulacaksa da bu ürün grubu altında bizden hangi ürün grubu sırasıyla talimatların dönülmesi beklenmektedir? AY: Diğer grubunun altında tekrar bir kategorizasyon olmayacak. Direk olarak sizin ürün tipi olarak diğer için gönderdiğiniz maks 8 adet fatura bilgisini müşterimize vereceğiz.


Talimat adedi ile ilgili bir sınır olacak mı? Özellikler kurumsal müşterilerin çok fazla sayıda talimatı olabiliyor. Max döneceğimiz bir talimat adedi var mı? Varsa bunu neye göre belirleyeceğiz? Örn. 10 adet talimatı dön diyorsa bu 10 adet nereye göre belirlenecek?
Talimatlar ürünlere göre gruplandırılacaktır.(CEPTEL, DOĞALGAZ, ELEKTRİK vb.) Her ürün grubu için 9 adet abonelik bilgisi talimat sorgulama servisinden dönülecek. AY: Talimatların ürünlere göre gruplanmasını bekliyoruz. Her ürün grubunda bize maksimum 8 adet talimatın gönderilmesini bekliyoruz. Burada ürün grubuna göre otomatik ödeme talimatı verilme tarihi geçmişten günümüze doğru sıralanarak ilk 8 fatura için servisten dönüş olması makul. 


Talimat giriş tarihi en esi olandan itibaren mi yoksa en yeni olandan itibaren mi AY: En eski olandan en yeniye doğru sıralanmalı


Hesap numarası okunacak mı? AY: IVR’da hesap numarası okunmayacak Okunacak ise müşteri genel hesap üzerinden ya da birden fazla hesap üzerinden talimat vermiş olabilir. Hepsini servisten dönmemizi mi bekliyorsunuz?


@Override
	public ResponseGetSubscriberRecordsWithAccounts getSubscriberRecordsWithAccounts( RequestGetSubscriberRecordsWithAccounts i_request ) throws HmnServiceException {

		ResponseGetSubscriberRecordsWithAccounts response = new ResponseGetSubscriberRecordsWithAccounts();
		// set the reference from request
		response.setReference( i_request != null ? i_request.getReference() : null );
		response.setClientUniqueReference( i_request != null ? i_request.getClientUniqueReference() : null );
		
		// validate request 
		ResponseStatusMsgDTO requestStatusMsg = PYMResponseUtils.validateRequest( i_request );
		
		// if request status msg is null, means request is valid
		if( requestStatusMsg == null ) {
			// Calling "get" method result from CoreBillPaymentsService.
			CoreServiceResultDTO<List<PaymentSubscriberAccountsDTO>> resultDTO = null;
			try {
				resultDTO = ServiceLocator.getPaymentSubscriberService().getSubscriberRecordsWithAccounts( i_request, 
						new CoreServiceBaseDataDTO( i_request.getChannelCode(), i_request.getAgentCode(), i_request
								.getReference(), null, i_request.getOperatingBranchCode(), i_request.getClientUniqueReference() ) );
			} catch( Exception e ) {
				String exceptionKey = UUID.randomUUID().toString();
				PYMLogUtil.logException( e, i_request, null, null, exceptionKey );
				PYMErrorUtils.throwGenericErrorServiceResult( "getSubscriberRecordsWithAccounts", e.getClass().getSimpleName(), exceptionKey, i_request.getReference() );
			}
 
			if( resultDTO != null ) {
				if( BillPaymentsConsts.RESPONSE_STATUS.SUCCESS.equals( resultDTO.getStatus() ) ) {
					if( resultDTO.getResult() != null ) {
						response.setSubscriberAccountsDTOList( resultDTO.getResult() );
					}
				}
				// pass the state coming from service 
				response.setStatus( resultDTO.getStatus() );
				response.setStatusMessage( resultDTO.getResponseMessage() );
			}
			else {
				// DTO can return null if one or more of the parameters are malformed or missing, so set error state
				response.setStatus( BillPaymentsConsts.RESPONSE_STATUS.ERROR );
				response.setStatusMessage( PYMResponseUtils.createResponseStatusDTO( EnumResponseCodes.GENERIC__REQUEST_PARAMS_INVALID.getValue(), null ) );
			}
		}
		else {
			// request is not valid, so set error state
			response.setStatus( BillPaymentsConsts.RESPONSE_STATUS.ERROR );
			response.setStatusMessage( requestStatusMsg );
		}
		return response;
	}



public class RequestGetSubscriberRecordsWithAccounts extends BaseBillPaymentRequest {

	private static final long serialVersionUID = -2147851444973597173L;

	private Integer customerNo;
	
	private String product;

	private String subscriberNo;
	
	private String institution;
	
	private String creditCartNumber;
	
	private String accountNo;
	
	private Boolean generalAccount;
	
	private Integer pageSize;
	
	private Integer pageNumber;





BU DATAFACADE DİYE BİR CLASSIMDAYDI



@Override
	public CoreServiceResultDTO<List<PaymentSubscriberAccountsDTO>> getSubscriberRecordsWithAccounts( RequestGetSubscriberRecordsWithAccounts i_request, CoreServiceBaseDataDTO i_requestSource ) throws HmnServiceException {

		CoreServiceResultDTO<List<PaymentSubscriberAccountsDTO>> resultDTO = new CoreServiceResultDTO<List<PaymentSubscriberAccountsDTO>>();
		
		List<PaymentSubscriberAccountsDTO> resultList = new ArrayList<PaymentSubscriberAccountsDTO>();
		
		// get the parameters -- start {
		String[] excludeProductList = null;
		Boolean isGetAll = Boolean.FALSE;
		// Validation for channel code.
		if( PYMChannelUtils.isValidChannelCode( i_requestSource.getChannelCode() ) ) {
			
			// Getting parameter code list.
			String[] parameterCodeList = CommonConsts.ParameterCodes.PARAMLIST__DATA_SERVICES__GET_SUBSCRIBER_AND_ACCOUNT_LIST;
			
			String[] parameterChannelCodeList = new String[]{ i_requestSource.getChannelCode() };
		
			Map<String, List<ChannelParametersDTO>> paramMap = PYMDtoUtils
					.getCoreChannelParametersInMapForm( ServiceLocator.getCoreBillPaymentsService()
							.getChannelParametersByCriteria( parameterCodeList, parameterChannelCodeList ) );
			
			// parse the parameters to set into query
			if( paramMap != null && paramMap.size() > 0 ) {
				List<ChannelParametersDTO> tempVal = null;
				
				// Get exclude product list from PYM_CHANNEL_PARAMETERS DB table for 'QRY_EXCL_PRDCT_GETSUBSACCLIST' parameter code.
				tempVal = paramMap.get( CommonConsts.ParameterCodes.QUERY_EXCLUDE_PRODUCTS_FOR_GETSUBSACCLIST );
				if( tempVal != null && tempVal.get( 0 ) != null && tempVal.get( 0 ).getParameterValue() != null ) {
					excludeProductList = tempVal.get( 0 ).getParameterValue().split( "," );

					if( excludeProductList.length == 0 ) {
						excludeProductList = null;
					} else {
						for( int i = 0; i < excludeProductList.length; i++ ) {
							excludeProductList[i] = excludeProductList[i] != null ? excludeProductList[i].trim() : null;
						}
					}
				}
			}
		}
		// get the parameters -- end }
		if(i_request.getPageNumber() == null || i_request.getPageSize()==null){
			i_request.setPageNumber(1);
			i_request.setPageSize(2000);
		}
		if(i_request.isGeneralAccount()==null || i_request.isGeneralAccount()){
			i_request.setGeneralAccount(Boolean.TRUE);
			isGetAll = Boolean.TRUE;
		}
		
		// Null check for request data.
		
			// paging is enabled
			if (i_request.getPageNumber()>0 && i_request.getPageSize()>0){
				// subscriber info with generic accounts only
				GetSubscriberRecordsWithAccountsTransformer subscriberAndAccountListWithGenericAccounts = new GetSubscriberRecordsWithAccountsTransformer(i_request, excludeProductList);
				QuerySupport.dynamicQuery(NQConsts.PYM_BILLPAYMENTS_GET_SUBSCRIBER_RECORDS_AND_ACCOUNTS.QUERY_NAME, subscriberAndAccountListWithGenericAccounts);
				resultList.addAll( subscriberAndAccountListWithGenericAccounts.getSubscriberList() );
				if(isGetAll){
					// subscriber info without generic accounts
					i_request.setGeneralAccount(Boolean.FALSE);
					GetSubscriberRecordsWithAccountsTransformer subscriberAndAccountListWithoutGenericAccounts =new GetSubscriberRecordsWithAccountsTransformer(i_request, excludeProductList);
					QuerySupport.dynamicQuery(NQConsts.PYM_BILLPAYMENTS_GET_SUBSCRIBER_RECORDS_AND_ACCOUNTS.QUERY_NAME, subscriberAndAccountListWithoutGenericAccounts);
					// merge subscriber lists
					resultList.addAll( subscriberAndAccountListWithoutGenericAccounts.getSubscriberList() );
				}							
			}

			
			if( resultList != null && resultList.size() > 0 ) {
				resultDTO.setStatus( BillPaymentsConsts.RESPONSE_STATUS.SUCCESS );
				resultDTO.setResponseMessage( PYMResponseUtils.createResponseStatusDTO( EnumResponseCodes.SUCCESS.getValue(), null ) );
				resultDTO.setResult( resultList );
			}
			else {
				resultDTO.setStatus( BillPaymentsConsts.RESPONSE_STATUS.ERROR );
				resultDTO.setResponseMessage( PYMResponseUtils.createResponseStatusDTO( EnumResponseCodes.AUTO_NO_AUTO_PAYMENT_ORDER_FOUND_FOR_CUSTOMER.getValue(), null ) );
				resultDTO.setResult( null );
			}

		return resultDTO;
	}


BURASIDA SERVİSİMDE..


 public static interface PYM_BILLPAYMENTS_GET_SUBSCRIBER_RECORDS_AND_ACCOUNTS {

        public static final String QUERY_NAME                       = "PYM_BILLPAYMENTS_GET_SUBSCRIBER_RECORDS_AND_ACCOUNTS";

        // Input Parameters
        public static final String IN__CUSTOMER_NO                  = "P_CUSTOMER_NO";
        public static final String IN__PRODUCT						= "P_PRODUCT";
        public static final String IN__INSTITUTION					= "P_INSTITUTION";
        public static final String IN__SUBSCRIBERNO					= "P_SUBSCRIBER_NO";
        public static final String IN__CREDITCARDNO					= "P_CREDITCARD_NO";
        public static final String IN__ACCOUNTNO					= "P_ACCOUNT_NO";
        public static final String IN__EXCLUDE_PRODUCT_LIST         = "P_EXCLUDE_PRODUCT_LIST";
        public static final String IN__GENERAL_ACCOUNT_ACTIVE       = "P_GENERAL_ACCOUNT_ACTIVE";
        public static final String IN__GENERAL_ACCOUNT_DEACTIVE     = "P_GENERAL_ACCOUNT_DEACTIVE";
        public static final String IN__PAGE_NUMBER          		= "P_PAGE_NUMBER";
        public static final String IN__PAGE_SIZE            		= "P_PAGE_SIZE";

        // Output Parameters
        // Subscriber output parameters
        public static final String OUT__PRODUCT                     = "O_PRODUCT";
        public static final String OUT__INSTITUTION                 = "O_INSTITUTION";
        public static final String OUT__SUBSCRIBER_NO               = "O_SUBSCRIBER_NO";
        public static final String OUT__ACCOUNT_NUMBER              = "O_ACCOUNT_NUMBER";
        public static final String OUT__CURRENCY                    = "O_CURRENCY";
        public static final String OUT__CUSTOMER_NO                 = "O_CUSTOMER_NO";
        public static final String OUT__INSTITUTION_SERVICE_TYPE    = "O_INSTITUTION_SERVICE_TYPE";
        public static final String OUT__ERROR_CODE                  = "O_ERROR_CODE";
        public static final String OUT__PAYMENT_ORDER_BRANCH_CODE   = "O_PAYMENT_ORDER_BRANCH_CODE";
        public static final String OUT__PAYMENT_ORDER_USER          = "O_PAYMENT_ORDER_USER";
        public static final String OUT__PAYMENT_ORDER_DATE          = "O_PAYMENT_ORDER_DATE";
        public static final String OUT__PAYMENT_ORDER_DELETED_BY    = "O_PAYMENT_ORDER_DELETED_BY";
        public static final String OUT__PAYMENT_ORDER_DELETE_DATE   = "O_PAYMENT_ORDER_DELETE_DATE";
        public static final String OUT__CUSTOMER_TYPE               = "O_CUSTOMER_TYPE";
        public static final String OUT__STATUS                      = "O_STATUS";
        public static final String OUT__PAYMENT_SELECTION           = "O_PAYMENT_SELECTION";
        public static final String OUT__CREDIT_CARD_NUMBER          = "O_CREDIT_CARD_NUMBER";
        public static final String OUT__CREDIT_CARD_EXPIRE_DATE     = "O_CREDIT_CARD_EXPIRE_DATE";
        public static final String OUT__SECONDARY_ACCOUNT_NUMBER    = "O_SECONDARY_ACCOUNT_NUMBER";
        public static final String OUT__SECONDARY_ACCOUNT_CURRENCY  = "O_SECONDARY_ACCOUNT_CURRENCY";
        public static final String OUT__THIRD_ACCOUNT_NUMBER        = "O_THIRD_ACCOUNT_NUMBER";
        public static final String OUT__THIRD_ACCOUNT_CURRENCY      = "O_THIRD_ACCOUNT_CURRENCY";
        public static final String OUT__CASH_ADVANCE                = "O_CASH_ADVANCE";
        public static final String OUT__LIMIT_AMOUNT                = "O_LIMIT_AMOUNT";
        public static final String OUT__ACCOUNT_BRANCH_CODE         = "O_ACCOUNT_BRANCH_CODE";
        public static final String OUT__BIN                         = "O_BIN";
        public static final String OUT__SUBSCRIBER_INFO1            = "O_SUBSCRIBER_INFO1";
        public static final String OUT__SUBSCRIBER_INFO2            = "O_SUBSCRIBER_INFO2";
        public static final String OUT__SUBSCRIBER_INFO3            = "O_SUBSCRIBER_INFO3";
        public static final String OUT__EMAIL_NOTIFICATION          = "O_EMAIL_NOTIFICATION";
        public static final String OUT__EXCLUDE_OVERDRAFT           = "O_EXCLUDE_OVERDRAFT";
        public static final String OUT__UPDATED_BY                  = "O_UPDATED_BY";
        public static final String OUT__UPDATE_DATE                 = "O_UPDATE_DATE";
        public static final String OUT__DESCRIPTION                 = "O_DESCRIPTION";
        public static final String OUT__DESCRIPTION2                = "O_DESCRIPTION2";
        public static final String OUT__DESCRIPTION3                = "O_DESCRIPTION3";
        public static final String OUT__DESCRIPTION4                = "O_DESCRIPTION4";
        public static final String OUT__DESCRIPTION5                = "O_DESCRIPTION5";
        public static final String OUT__PAYMENT_ORDER_TYPE          = "O_PAYMENT_ORDER_TYPE";
        public static final String OUT__PAYMENT_FREQUENCY           = "O_PAYMENT_FREQUENCY";
        public static final String OUT__BTHCREDIT_ACCOUNT_NUMBER    = "O_BTHCREDIT_ACCOUNT_NUMBER";
        public static final String OUT__TEMPLATE_TYPE               = "O_TEMPLATE_TYPE";
        public static final String OUT__CARD_TYPE                   = "O_CARD_TYPE";
        public static final String OUT__TRANSACTION_INFO            = "O_TRANSACTION_INFO";
        public static final String OUT__YKBDATA                     = "O_YKBDATA";
        public static final String OUT__COMMISSION_RATE_LIMIT       = "O_COMMISSION_RATE_LIMIT";
        public static final String OUT__COMMISSION_RATE_ADAT        = "O_COMMISSION_RATE_ADAT";
        public static final String OUT__COMMISSION_RATE_ENDORSEMENT = "O_COMMISSION_RATE_ENDORSEMENT";
        public static final String OUT__SUPPORT_CODE                = "O_SUPPORT_CODE";
        public static final String OUT__DEBIT_DATE                  = "O_DEBIT_DATE";
        public static final String OUT__RISK_AMOUNT                 = "O_RISK_AMOUNT";
        public static final String OUT__COMMISSION_AMOUNT           = "O_COMMISSION_AMOUNT";
        public static final String OUT__COMMISSION_RATE             = "O_COMMISSION_RATE";
        public static final String OUT__ORDER_NAME             		= "O_ORDER_NAME";
        // Template information output parameters.		
        public static final String OUT__SEQUENCE_NO                 = "O_SEQUENCE_NO";
        public static final String OUT__ACCOUNT_NO                  = "O_ACCOUNT_NO";
    }

BU CONSTANT TARAFINDA 




SELECT * FROM (
      SELECT a.*,ROWNUM SELECTED_ROW_NUMBER,
             (select count(*) FROM T_OTO_ABONE S, SABLON T
              WHERE S.STATU <> 'D'
              AND S.MUSTERINO = T.CIFNO(+)
              @dynamic[ P_CUSTOMER_NO, AND S.MUSTERINO = :P_CUSTOMER_NO ]
              @dynamic[ P_PRODUCT, AND S.URUN = :P_PRODUCT ]
              @dynamic[ P_INSTITUTION, AND S.KURUM = :P_INSTITUTION ]
              @dynamic[ P_SUBSCRIBER_NO, AND S.ABONENO = :P_SUBSCRIBER_NO ]
              @dynamic[ P_ACCOUNT_NO, AND T.HESAPNO = :P_ACCOUNT_NO  ]
              @dynamic[ P_CREDITCARD_NO, AND S.KARTNO = :P_CREDITCARD_NO ]
              @dynamic[ P_EXCLUDE_PRODUCT_LIST, AND S.URUN NOT IN ( :P_EXCLUDE_PRODUCT_LIST ) ]
              @dynamic[ P_GENERAL_ACCOUNT_ACTIVE, AND S.SABLONTIPI = 'G' AND T.ABONENO = 'GENELHESAP' AND T.KURUM = 'GENELHESAP' AND T.URUN = 'GENELHESAP' ]
              @dynamic[ P_GENERAL_ACCOUNT_DEACTIVE, AND S.SABLONTIPI <> 'G' AND S.ABONENO = T.ABONENO(+) AND S.KURUM = T.KURUM(+) AND S.URUN = T.URUN(+) ]) TOTAL_PACKAGE_COUNT
     FROM (
              SELECT DISTINCT S.URUN O_PRODUCT, S.KURUM O_INSTITUTION, S.ABONENO O_SUBSCRIBER_NO,
              S.HESAPNO O_ACCOUNT_NUMBER, S.DOVIZ O_CURRENCY, S.MUSTERINO O_CUSTOMER_NO,
              S.SRVTIPI O_INSTITUTION_SERVICE_TYPE, S.HATAKODU O_ERROR_CODE, S.GIRISSUBE O_PAYMENT_ORDER_BRANCH_CODE,
              S.GIRENUSER O_PAYMENT_ORDER_USER, S.GIRISTARIH O_PAYMENT_ORDER_DATE, S.SILENUSER O_PAYMENT_ORDER_DELETED_BY,
              S.SILMETARIH O_PAYMENT_ORDER_DELETE_DATE, S.MUSTERITIPI O_CUSTOMER_TYPE, S.STATU O_STATUS,
              S.ODEMESECIMI O_PAYMENT_SELECTION, S.KARTNO O_CREDIT_CARD_NUMBER, S.SONKULLANMA O_CREDIT_CARD_EXPIRE_DATE,
              S.IKINCILHESAPNO O_SECONDARY_ACCOUNT_NUMBER, S.IKINCILDOVIZ O_SECONDARY_ACCOUNT_CURRENCY, S.UCUNCUHESAPNO O_THIRD_ACCOUNT_NUMBER,
              S.UCUNCUDOVIZ O_THIRD_ACCOUNT_CURRENCY, S.NAKITAVANS O_CASH_ADVANCE, S.LIMITTUTARI O_LIMIT_AMOUNT,
              S.SUBE O_ACCOUNT_BRANCH_CODE, S.BIN O_BIN, S.ABBILGI1 O_SUBSCRIBER_INFO1,
              S.ABBILGI2 O_SUBSCRIBER_INFO2, S.ABBILGI3 O_SUBSCRIBER_INFO3, S.EMAILBILDIRIM O_EMAIL_NOTIFICATION,
              S.ODHARIC O_EXCLUDE_OVERDRAFT, S.CHGUSER O_UPDATED_BY, S.CHGTARIH O_UPDATE_DATE,
              S.ACIKLAMA O_DESCRIPTION, S.ACIKLAMA2 O_DESCRIPTION2, S.ACIKLAMA3 O_DESCRIPTION3,
              S.ACIKLAMA4 O_DESCRIPTION4, S.ACIKLAMA5 O_DESCRIPTION5, S.TALIMATTIPI O_PAYMENT_ORDER_TYPE,
              S.ODMFREKANS O_PAYMENT_FREQUENCY, S.BTHHESAPNO O_BTHCREDIT_ACCOUNT_NUMBER, S.SABLONTIPI O_TEMPLATE_TYPE,
              S.KARTTURU O_CARD_TYPE, S.ISLBIL O_TRANSACTION_INFO, S.YKBDATA O_YKBDATA,
              S.KOMORANLIMIT O_COMMISSION_RATE_LIMIT, S.KOMORANADAT O_COMMISSION_RATE_ADAT, S.KOMORANCIRO O_COMMISSION_RATE_ENDORSEMENT,
              S.DSTKODU O_SUPPORT_CODE, S.BORCTARIH O_DEBIT_DATE, S.RISKTUTARI O_RISK_AMOUNT,
              S.KOMISYONTUTAR O_COMMISSION_AMOUNT, S.KOMISYONORAN O_COMMISSION_RATE,S.ORDERNAME O_ORDER_NAME,
              T.HESAPNO O_ACCOUNT_NO, T.SIRANO O_SEQUENCE_NO FROM T_OTO_ABONE S, SABLON T
              WHERE S.STATU <> 'D'
              AND S.MUSTERINO = T.CIFNO(+)
              @dynamic[ P_CUSTOMER_NO, AND S.MUSTERINO = :P_CUSTOMER_NO ]
              @dynamic[ P_PRODUCT, AND S.URUN = :P_PRODUCT ]
              @dynamic[ P_INSTITUTION, AND S.KURUM = :P_INSTITUTION ]
              @dynamic[ P_SUBSCRIBER_NO, AND S.ABONENO = :P_SUBSCRIBER_NO ]
              @dynamic[ P_ACCOUNT_NO, AND T.HESAPNO = :P_ACCOUNT_NO  ]
              @dynamic[ P_CREDITCARD_NO, AND S.KARTNO = :P_CREDITCARD_NO ]
              @dynamic[ P_EXCLUDE_PRODUCT_LIST, AND S.URUN NOT IN ( :P_EXCLUDE_PRODUCT_LIST ) ]
              @dynamic[ P_GENERAL_ACCOUNT_ACTIVE, AND S.SABLONTIPI = 'G' AND T.ABONENO = 'GENELHESAP' AND T.KURUM = 'GENELHESAP' AND T.URUN = 'GENELHESAP' ]
              @dynamic[ P_GENERAL_ACCOUNT_DEACTIVE, AND S.SABLONTIPI <> 'G' AND S.ABONENO = T.ABONENO(+) AND S.KURUM = T.KURUM(+) AND S.URUN = T.URUN(+) ]
    ) a
        WHERE ROWNUM < ((:P_PAGE_NUMBER * :P_PAGE_SIZE) + 1)
    )
    WHERE SELECTED_ROW_NUMBER >= (((:P_PAGE_NUMBER - 1) * :P_PAGE_SIZE) + 1)


BUDA BENİM SQLİM VERİ TABANINDAKİ
