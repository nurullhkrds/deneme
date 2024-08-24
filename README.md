@Service
@RequiredArgsConstructor
public class PaymentNotificationServiceImpl implements PaymentNotificationService {

    private static final Logger logger = LoggerFactory.getLogger(PaymentNotificationServiceImpl.class);
	
	private static final String SERVICE_NAME = "PaymentNotificationServiceImpl";

	@Value("${parameter.creditCardProvisionACKEvent.parameters.notificationMaxTryCount:3}")
	private Integer creditCardProvisionACKNotificationMaxTryCount;
	
	@Value("${parameter.creditCardReverseProvisionEvent.parameters.notificationMaxTryCount:3}")
	private Integer creditCardReverseProvisionNotificationMaxTryCount;
	
	private final PaymentNotificationRepository repository;
	private final PaymentNotificationMapper mapper;
    private final LoggingService loggingService;
    private final SwtSwitchIntegrationService cardProvisionService;
    private final PaymentService paymentService;

    
	@Override
	@Transactional
	public PaymentNotification findPaymentNotificationWithLock(Long paymentNotificationId) {
		if (paymentNotificationId == null) {
			return null;
		}

		Optional<PaymentNotification> findById = repository.findById(paymentNotificationId);

		return findById.isPresent() ? findById.get() : null;
	}

	@Override
	@Transactional
	public void updatePaymentNotification(PaymentNotification paymentNotification) {
		repository.save(paymentNotification);		
	}
	
	@Override
	public PaymentNotificationDTO insertPaymentNotification(PaymentNotificationDTO paymentNotificationDTO) {
		PaymentNotification paymentNotification = repository.save(mapper.toEntity(paymentNotificationDTO));
		return mapper.toDTO(paymentNotification);
	}
	
	
	@Override
	@Transactional(noRollbackFor = Exception.class)
	public void sendAckForCreditCardProvision(Long paymentId,Long paymentNotificationId, boolean isBatch) throws BusinessException {

		PaymentDTO paymentDTO = getPayment(paymentId);

		ServiceLogDTO serviceLogDTO = prepareSendAckForCreditCardProvisionLog(paymentDTO.getCreditCardNo(),
				paymentDTO.getSubscriberNo(), paymentDTO.getInstitutionId(),paymentDTO.getInstitutionDebtTypeId(),paymentDTO.getChannelTransactionId(),
				paymentDTO.getChannelSessionId());
		
		PaymentNotification paymentNotification = findPaymentNotificationWithLock(paymentNotificationId);
		
		Stopwatch watch = Stopwatch.createStarted();


		try {
			
			validatePaymentNotification(paymentNotification, isBatch,
					creditCardProvisionACKNotificationMaxTryCount);

			CardProvisionAcknowledgeRequest cardProvisionAcknowledgeReq = prepareCardProvisionAcknowledgeRequest(
					paymentDTO);
			
			serviceLogDTO.setRequestData(StringUtils.substring(JsonUtil.convertObjectToJsonStringWithoutException(cardProvisionAcknowledgeReq),0,4000));
			

			CardProvisionAcknowledgeResponse doProvisionAcknowledge = cardProvisionService.doProvisionAcknowledge(cardProvisionAcknowledgeReq, paymentDTO.getChannelTransactionId(),
					paymentDTO.getChannelSessionId());
			
			Long elapsedTime = watch.stop().elapsed(TimeUnit.MILLISECONDS);
			
			serviceLogDTO.setElapsedTime(elapsedTime);
			serviceLogDTO.setResponseData(StringUtils.substring(JsonUtil.convertObjectToJsonStringWithoutException(doProvisionAcknowledge),0,4000));
			serviceLogDTO.setResultCode(doProvisionAcknowledge.getResultCode());
			serviceLogDTO.setResultType(EnumLoggingResultType.SUCCESS);
			serviceLogDTO.setReturnCode(0);
			
			paymentNotification.setNotificationStatus(EnumPaymentNotificationStatu.SUCCESS);
			paymentNotification.setResponseCode(
					doProvisionAcknowledge.getResultCode());
			paymentNotification.setResponseMessage(
					PaymentExceptions.AccountingExceptions.CREDIT_CARD_PROVISION_ACK_NOTIFICATION_SUCCESS.getErrorMessage());
			paymentNotification.setNotificationDate(LocalDateTime.now());
			paymentNotification.setRetryCount(paymentNotification.getRetryCount() + 1);
			paymentNotification.setUpdatedBy(isBatch ? BillTransactionConstant.BATCH : BillTransactionConstant.SYSTEM);
			paymentNotification.setUpdateDate(LocalDateTime.now());

			repository.save(paymentNotification);

		} catch (Exception ex) {
			
			if (watch.isRunning()) {
                watch.stop();
            }
			
			serviceLogDTO.setException(ex);	
			serviceLogDTO.setElapsedTime(watch.elapsed(TimeUnit.MILLISECONDS));

			logger.error(
					"Payment Id: '{}' olan kayıt için credit card provision ack bildirilirken bilinmeyen hata olustu. Hata Detay:'{}'",
					paymentDTO.getId(), ExceptionUtils.getStackTrace(ex));

			paymentNotification.setNotificationStatus(EnumPaymentNotificationStatu.ERROR);
			paymentNotification.setRetryCount(paymentNotification.getRetryCount() + 1);
			if(ex.getCause() instanceof MicroException) {
				MicroException mc = (MicroException) ex.getCause(); 
				paymentNotification.setResponseCode(mc.getExceptionData().getErrorCode().toString());
				paymentNotification.setResponseMessage(mc.getExceptionData().getErrorMessage());
				serviceLogDTO.setException(mc);
			}
			else {
				paymentNotification.setResponseCode("-999");
				paymentNotification.setResponseMessage(ex.getLocalizedMessage());
			}
			paymentNotification.setUpdatedBy(isBatch ? BillTransactionConstant.BATCH : BillTransactionConstant.SYSTEM);
			paymentNotification.setUpdateDate(LocalDateTime.now());

			repository.save(paymentNotification);

			if (!isBatch) {
				throw new ReQueueException(); /** Tekrar RabbitMQ'ya bırakır. */
			}

		}
		
	finally {
		loggingService.saveServiceLog(serviceLogDTO);
		
	}
		

	}
	
	 
	private void validatePaymentNotification(PaymentNotification paymentNotification, boolean isBatch,
			Integer maxTryCount) throws BillException {

		BillValidationUtil.validateCondition(paymentNotification != null,
				EnumBillResult.BILL_PAYMENT_NOTIFICATION_NOT_FOUND, BillTransactionConstant.APP_NAME);

		BillValidationUtil.validateCondition(
				!EnumPaymentNotificationStatu.SUCCESS.getValue().equals(paymentNotification.getNotificationStatus()),
				EnumBillResult.BILL_PAYMENT_NOTIFICATION_ALREADY_NOTIFIED, BillTransactionConstant.APP_NAME);

		/**
		 * RabbitMQ bildirememiş. Batch çalışıp bildirecek. Bu yüzden try_count
		 * kontrolleri atlanır.
		 */
		/*
		 * if (isBatch) {
		 * 
		 * }
		 */

		BillValidationUtil.validateCondition(paymentNotification.getRetryCount() < maxTryCount,
				EnumBillResult.BILL_PAYMENT_NOTIFICATION_TRY_COUNT_EXCEEDED, BillTransactionConstant.APP_NAME);

	}
		
	private ServiceLogDTO prepareSendAckForCreditCardProvisionLog(String creditCardNumber, String subscriberNo,
			Long institutionId, Long institutionDebtTypeId, String channelTransactionId, String channelSessionId) {
		ServiceLogDTO serviceLogDTO = new ServiceLogDTO(BillTransactionConstant.APP_NAME, SERVICE_NAME,
				"sendAckForCreditCardProvision");
		serviceLogDTO.setChannelCode(EnumChannel.SYSTEM.getValue());
		serviceLogDTO.setServiceDirection(EnumServiceDirectionType.INTERNAL_TO_INTERNAL.getValue());
		serviceLogDTO.setChannelTransactionId(channelTransactionId);
		serviceLogDTO.setChannelSessionId(channelSessionId);
		serviceLogDTO.setKey1(creditCardNumber);
		serviceLogDTO.setSubscriberNo(subscriberNo);
		serviceLogDTO.setInstitutionId(institutionId);
		serviceLogDTO.setInstitutionDebtTypeId(institutionDebtTypeId);
		serviceLogDTO.setBranchCode("925");
		serviceLogDTO.setCreatedBy(BillTransactionConstant.SYSTEM);
		return serviceLogDTO;
	}
		
	private CardProvisionAcknowledgeRequest prepareCardProvisionAcknowledgeRequest(PaymentDTO payment) {
		CardProvisionAcknowledgeRequest acknowledgeRequest = new CardProvisionAcknowledgeRequest();

		if (payment.getProvisionRequestId().contains("PYMD")) {
			acknowledgeRequest
					.setRequestId(CommonUtils.generateCreditCardProvisionRequestId(payment.getChannelCode(), true));
		} else {
			acknowledgeRequest
					.setRequestId(CommonUtils.generateCreditCardProvisionRequestId(payment.getChannelCode(), false));
		}

		acknowledgeRequest.setRequestDate(
				DateUtils.formatLocalDateTime(LocalDateTime.now(), DateUtils.DATE_FORMAT_YYYY_MM_DD_HH_MM_SS_SSS));
		acknowledgeRequest.setOriginalRequestId(payment.getProvisionRequestId());
		acknowledgeRequest.setCardNo(payment.getCreditCardNo());
		acknowledgeRequest.setChannelCode(payment.getChannelCode());
		acknowledgeRequest.setConsumerChannelCode(payment.getChannelCode());
		acknowledgeRequest.setSessionId(payment.getChannelSessionId());
		
		 
		List<KeyValueDto> additionalTransactionInfoList = new ArrayList<>();

		KeyValueDto additionalInvoiceNoInfo = new KeyValueDto();
		additionalInvoiceNoInfo.setKey("invoiceNo");
		additionalInvoiceNoInfo.setValue(payment.getBillNo());
		additionalTransactionInfoList.add(additionalInvoiceNoInfo);

		KeyValueDto additionalContractNoInfo = new KeyValueDto();
		additionalContractNoInfo.setKey("contractNo");
		additionalContractNoInfo.setValue(payment.getSubscriberNo());
		additionalTransactionInfoList.add(additionalContractNoInfo);

		acknowledgeRequest.setAdditionalTransactionInfoList(additionalTransactionInfoList);

		
		return acknowledgeRequest;
	}

	private PaymentDTO getPayment(Long paymentId) throws BusinessException {
		return paymentService.getPayment(paymentId);
	}

	private PaymentCancelDTO getPaymentCancel(Long paymentId) throws BusinessException {
		return paymentService.getPaymentCancel(paymentId);
	}

	@Override
	@Transactional(noRollbackFor = Exception.class)
	public void creditCardReverseProvision(Long paymentId, Long paymentNotificationId, Long paymentCancelId,
			boolean isBatch) throws BusinessException {

		PaymentDTO paymentDTO = getPayment(paymentId);

		PaymentCancelDTO paymentCancelDTO = getPaymentCancel(paymentDTO.getId());

		ServiceLogDTO serviceLogDTO = prepareCreditCardProvisionLog(paymentDTO.getCreditCardNo(),
				paymentDTO.getSubscriberNo(), paymentDTO.getInstitutionId(), paymentDTO.getInstitutionDebtTypeId(),
				paymentDTO.getChannelTransactionId(), paymentDTO.getChannelSessionId());

		PaymentNotification paymentNotification = findPaymentNotificationWithLock(paymentNotificationId);

		try {

			validatePaymentNotification(paymentNotification, Boolean.FALSE,
					creditCardReverseProvisionNotificationMaxTryCount);

			CardReverseProvisionRequest cardReverseProvisionReq = prepareCardReverseProvisionRequest(paymentDTO,
					paymentCancelDTO);

			Stopwatch watch = Stopwatch.createStarted();

			CardReverseProvisionResponse doReverseProvision = cardProvisionService
					.doReverseProvision(cardReverseProvisionReq);

			Long elapsedTime = watch.stop().elapsed(TimeUnit.MILLISECONDS);

			serviceLogDTO.setElapsedTime(elapsedTime);
			serviceLogDTO.setResponseData(StringUtils
					.substring(JsonUtil.convertObjectToJsonStringWithoutException(doReverseProvision), 0, 4000));
			serviceLogDTO.setRequestData(StringUtils
					.substring(JsonUtil.convertObjectToJsonStringWithoutException(cardReverseProvisionReq), 0, 4000));
			serviceLogDTO.setResultCode(doReverseProvision.getResultCode());
			serviceLogDTO.setResultType(EnumLoggingResultType.SUCCESS);
			serviceLogDTO.setReturnCode(0);

			paymentNotification.setNotificationStatus(EnumPaymentNotificationStatu.SUCCESS);
			paymentNotification.setResponseCode(
					doReverseProvision.getResultCode());
			paymentNotification.setResponseMessage(
					PaymentExceptions.AccountingExceptions.CREDIT_CARD_REVERSE_PROVISION_NOTIFICATION_SUCCESS
							.getErrorMessage());
			paymentNotification.setNotificationDate(LocalDateTime.now());
			paymentNotification.setRetryCount(paymentNotification.getRetryCount() + 1);
			paymentNotification.setUpdatedBy(BillTransactionConstant.SYSTEM_GEN);
			paymentNotification.setUpdateDate(LocalDateTime.now());

			repository.save(paymentNotification);

		} catch (Exception ex) {
			serviceLogDTO.setException(ex);
			loggingService.saveServiceLog(serviceLogDTO);

			logger.error(
					"Reverse Provision Request Id: '{}' olan kayıt için credit card provision reverse bildirilirken bilinmeyen hata olustu. Hata Detay:'{}'",
					paymentCancelDTO.getReverseProvisionRequestId(), ExceptionUtils.getStackTrace(ex));

			if (ex instanceof MicroException) {
				MicroException mc = (MicroException) ex;
				paymentNotification.setResponseCode(mc.getExceptionData().getErrorCode().toString());
				paymentNotification.setResponseMessage(mc.getExceptionData().getErrorMessage());
			} else {
				paymentNotification.setResponseCode("-999");
				paymentNotification.setResponseMessage(ex.getLocalizedMessage());
			}

			paymentNotification.setNotificationStatus(EnumPaymentNotificationStatu.ERROR);
			paymentNotification.setRetryCount(paymentNotification.getRetryCount() + 1);
			paymentNotification.setUpdatedBy(BillTransactionConstant.SYSTEM);
			paymentNotification.setUpdateDate(LocalDateTime.now());

			repository.save(paymentNotification);

		}

		finally {
			loggingService.saveServiceLog(serviceLogDTO);

		}

	}

	private CardReverseProvisionRequest prepareCardReverseProvisionRequest(PaymentDTO paymentDTO,
			PaymentCancelDTO paymentCancelDTO) {
		CardReverseProvisionRequest cardReverseProvisionRequest = new CardReverseProvisionRequest();
		cardReverseProvisionRequest.setAmount(paymentDTO.getAmount());
		cardReverseProvisionRequest.setCardNo(paymentDTO.getCreditCardNo());
		cardReverseProvisionRequest.setChannelCode(paymentCancelDTO.getChannelCode());
		cardReverseProvisionRequest.setConsumerChannelCode(paymentCancelDTO.getChannelCode());
		cardReverseProvisionRequest.setOriginalRequestId(paymentDTO.getProvisionRequestId());
		cardReverseProvisionRequest.setRequestDate(
				DateUtils.formatLocalDateTime(LocalDateTime.now(), DateUtils.DATE_FORMAT_YYYY_MM_DD_HH_MM_SS_SSS));
		cardReverseProvisionRequest.setRequestId(paymentCancelDTO.getReverseProvisionRequestId());
		cardReverseProvisionRequest.setSessionId(paymentCancelDTO.getChannelTransactionId());

		return cardReverseProvisionRequest;

	}

	private ServiceLogDTO prepareCreditCardProvisionLog(String creditCardNumber, String subscriberNo,
			Long institutionId, Long institutionDebtTypeId, String channelTransactionId, String channelSessionId) {
		ServiceLogDTO serviceLogDTO = new ServiceLogDTO(BillTransactionConstant.APP_NAME, SERVICE_NAME,
				"creditCardReverseProvision");
		serviceLogDTO.setCreatedBy(BillTransactionConstant.SYSTEM);
		serviceLogDTO.setChannelCode(EnumChannel.SYSTEM.getValue());
		serviceLogDTO.setServiceDirection(EnumServiceDirectionType.INTERNAL_TO_INTERNAL.getValue());
		serviceLogDTO.setChannelTransactionId(channelTransactionId);
		serviceLogDTO.setChannelSessionId(channelSessionId);
		serviceLogDTO.setKey1(creditCardNumber);
		serviceLogDTO.setSubscriberNo(subscriberNo);
		serviceLogDTO.setInstitutionId(institutionId);
		serviceLogDTO.setInstitutionDebtTypeId(institutionDebtTypeId);
		serviceLogDTO.setBranchCode("925");
		serviceLogDTO.setCreatedBy(BillTransactionConstant.SYSTEM);
		return serviceLogDTO;
	}

}
 BU SINIFNIN 


"@ExtendWith(MockitoExtension.class)
class PaymentNotificationServiceTest {

    private PaymentNotificationService paymentNotificationService;

    @Mock
    private PaymentNotificationRepository repository;

    @Mock
    private PaymentNotificationMapper mapper;

    @Mock
    private LoggingService loggingService;

    @Mock
    private SwtSwitchIntegrationService cardProvisionService;

    @Mock
    private PaymentService paymentService;


    @BeforeEach
    void setUp() {
        paymentNotificationService = new PaymentNotificationServiceImpl(repository, mapper, loggingService, cardProvisionService, paymentService);
    }

    @Test
    void testInsertPaymentNotification() {
        when(repository.save(any(PaymentNotification.class))).thenReturn(new PaymentNotification());
        when(mapper.toEntity(any(PaymentNotificationDTO.class))).thenReturn(new PaymentNotification());
        when(mapper.toDTO(any(PaymentNotification.class))).thenReturn(new PaymentNotificationDTO());
        PaymentNotificationDTO paymentNotificationDTO = new PaymentNotificationDTO();
        PaymentNotificationDTO dto = paymentNotificationService.insertPaymentNotification(paymentNotificationDTO);
        assertNotNull(dto);
        verify(repository, Mockito.atLeast(1)).save(any(PaymentNotification.class));
        verify(mapper, Mockito.atLeast(1)).toEntity(any(PaymentNotificationDTO.class));
        verify(mapper, Mockito.atLeast(1)).toDTO(any(PaymentNotification.class));
    }

    @Test
    void testFindPaymentNotificationWithLock() {
        when(repository.findById(anyLong())).thenReturn(Optional.of(new PaymentNotification()));
        PaymentNotification dto = paymentNotificationService.findPaymentNotificationWithLock(1L);
        assertNotNull(dto);
        verify(repository, Mockito.atLeast(1)).findById(anyLong());
    }

    @Test
    void testUpdatePaymentNotification() {
        when(repository.save(any(PaymentNotification.class))).thenReturn(new PaymentNotification());
        Assertions.assertDoesNotThrow(()->{
            paymentNotificationService.updatePaymentNotification(new PaymentNotification());
        });
        verify(repository, Mockito.atLeast(1)).save(any(PaymentNotification.class));
    }

    @Test
    void testCreditCardReverseProvision() throws BusinessException, ServiceCallException {
        PaymentDTO t = new PaymentDTO();
        t.setProvisionRequestId("1");
        t.setChannelTransactionId("1");
        t.setChannelSessionId("1");
        t.setId(1L);
        when(paymentService.getPayment(anyLong())).thenReturn(t);
        when(paymentService.getPaymentCancel(anyLong())).thenReturn(new PaymentCancelDTO());
        PaymentNotification value = new PaymentNotification();
        value.setRetryCount(1);
        when(repository.findById(anyLong())).thenReturn(Optional.of(value));
        when(repository.save(any(PaymentNotification.class))).thenReturn(new PaymentNotification());
        when(cardProvisionService
                .doReverseProvision(any(CardReverseProvisionRequest.class))).thenReturn(new CardReverseProvisionResponse());
        mockFieldValue(paymentNotificationService, "creditCardReverseProvisionNotificationMaxTryCount", 3);
        Assertions.assertDoesNotThrow(()->{
            paymentNotificationService.creditCardReverseProvision(1L, 1L,1L,true);
        });
        verify(paymentService, Mockito.atLeast(1)).getPayment(anyLong());
        verify(paymentService, Mockito.atLeast(1)).getPaymentCancel(anyLong());
        verify(repository, Mockito.atLeast(1)).findById(anyLong());
        verify(repository, Mockito.atLeast(1)).save(any(PaymentNotification.class));
        verify(cardProvisionService, Mockito.atLeast(1)).doReverseProvision(any(CardReverseProvisionRequest.class));
    }

    @Test
    void testSendAckForCreditCardProvision() throws BusinessException, ServiceCallException {
        PaymentDTO t = new PaymentDTO();
        t.setProvisionRequestId("1");
        t.setChannelTransactionId("1");
        t.setChannelSessionId("1");
        when(paymentService.getPayment(anyLong())).thenReturn(t);
        PaymentNotification value = new PaymentNotification();
        value.setRetryCount(1);
        when(repository.findById(anyLong())).thenReturn(Optional.of(value));
        when(cardProvisionService.doProvisionAcknowledge(any(CardProvisionAcknowledgeRequest.class), anyString(),anyString())).thenReturn(new CardProvisionAcknowledgeResponse());
        when(repository.save(any(PaymentNotification.class))).thenReturn(new PaymentNotification());
        mockFieldValue(paymentNotificationService, "creditCardProvisionACKNotificationMaxTryCount", 3);
        Assertions.assertDoesNotThrow(()->{
            paymentNotificationService.sendAckForCreditCardProvision(1L,1L,true);
        });
        verify(paymentService, Mockito.atLeast(1)).getPayment(anyLong());
        verify(repository, Mockito.atLeast(1)).findById(anyLong());
        verify(cardProvisionService, Mockito.atLeast(1)).doProvisionAcknowledge(any(CardProvisionAcknowledgeRequest.class), anyString(),anyString());
        verify(repository, Mockito.atLeast(1)).save(any(PaymentNotification.class));
    }

    private void mockFieldValue(Object targetObject, String fieldName, Object fieldValue) {
        ReflectionTestUtils.setField(targetObject, fieldName, fieldValue);
    }
}
" TESTİ BBUDUR FAKATT 


"} catch (Exception ex) {
			
			if (watch.isRunning()) {
                watch.stop();
            }
			
			serviceLogDTO.setException(ex);	
			serviceLogDTO.setElapsedTime(watch.elapsed(TimeUnit.MILLISECONDS));

			logger.error(
					"Payment Id: '{}' olan kayıt için credit card provision ack bildirilirken bilinmeyen hata olustu. Hata Detay:'{}'",
					paymentDTO.getId(), ExceptionUtils.getStackTrace(ex));

			paymentNotification.setNotificationStatus(EnumPaymentNotificationStatu.ERROR);
			paymentNotification.setRetryCount(paymentNotification.getRetryCount() + 1);
			if(ex.getCause() instanceof MicroException) {
				MicroException mc = (MicroException) ex.getCause(); 
				paymentNotification.setResponseCode(mc.getExceptionData().getErrorCode().toString());
				paymentNotification.setResponseMessage(mc.getExceptionData().getErrorMessage());
				serviceLogDTO.setException(mc);
			}
			else {
				paymentNotification.setResponseCode("-999");
				paymentNotification.setResponseMessage(ex.getLocalizedMessage());
			}
			paymentNotification.setUpdatedBy(isBatch ? BillTransactionConstant.BATCH : BillTransactionConstant.SYSTEM);
			paymentNotification.setUpdateDate(LocalDateTime.now());

			repository.save(paymentNotification);

			if (!isBatch) {
				throw new ReQueueException(); /** Tekrar RabbitMQ'ya bırakır. */
			}

		}" BU TARAF VE "
		} catch (Exception ex) {
			serviceLogDTO.setException(ex);
			loggingService.saveServiceLog(serviceLogDTO);

			logger.error(
					"Reverse Provision Request Id: '{}' olan kayıt için credit card provision reverse bildirilirken bilinmeyen hata olustu. Hata Detay:'{}'",
					paymentCancelDTO.getReverseProvisionRequestId(), ExceptionUtils.getStackTrace(ex));

			if (ex instanceof MicroException) {
				MicroException mc = (MicroException) ex;
				paymentNotification.setResponseCode(mc.getExceptionData().getErrorCode().toString());
				paymentNotification.setResponseMessage(mc.getExceptionData().getErrorMessage());
			} else {
				paymentNotification.setResponseCode("-999");
				paymentNotification.setResponseMessage(ex.getLocalizedMessage());
			}

			paymentNotification.setNotificationStatus(EnumPaymentNotificationStatu.ERROR);
			paymentNotification.setRetryCount(paymentNotification.getRetryCount() + 1);
			paymentNotification.setUpdatedBy(BillTransactionConstant.SYSTEM);
			paymentNotification.setUpdateDate(LocalDateTime.now());

			repository.save(paymentNotification);

		}" BU TARAF
