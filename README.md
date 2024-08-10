public class BillPaymentReverseProcess extends AbstractProcess {
    
	private Long billId;
	private Long contractNo;
	private AccountingService accountingService;
    private CreateReverseAccountingResultDTO createReverseAccountingResultDTO;
    private PaymentService paymentService;
    private PaymentDTO paymentDTO;
    private PaymentCancelDTO paymentCancelDTO;
    private PaymentNotificationService paymentNotificationService;
    private ApplicationEventPublisher eventPublisher;
    private PaymentEventPublisher paymentEventPublisher;
    private List<PaymentNotificationDTO> insertedPaymentNotificationDTOList;
    private InstitutionFeatureService institutionFeatureService;
    private ChannelService channelService;
    
    private PaymentUtilImpl paymentUtilImpl;    
    private boolean	isFomOperationEnabled;



	@Override
	public void executeProcess() throws BillException {
		addProcessStep(new GatherData());
		addProcessStep(new FetchBillRecord());
		addProcessStep(new CheckInstitution());
		addProcessStep(new CheckIsReversible());
		addProcessStep(new OnlineCheckIsReversible());
		addProcessStep(new DoAccounting());
		addProcessStep(new InsertPaymentReverseRecord());
		addProcessStep(new UpdateCustomerPaymentLimit());
		addProcessStep(new UpdatePaymentRecord());
		addProcessStep(new InsertPaymentCancelNotificationRecord());
		addProcessStep(new PublishBillPaymentCancelEvent());
		
		executeSteps();
		prepareLogVariable();
	}

	@Override
	protected void prepareExecutionOutput() {
        executionOutput = new BillPaymentReverseProcessOutput();
        executionOutput.setResult(error);
        /*
         * TODO: servisten donecek olan degerler burada yer alacak
         * 
         */


	}

	private class GatherData implements ProcessStep {

		@Override
		public void executeStep() throws BillException {
            billId = (Long) dataPack.get(ProcessDataPackKey.BILL_ID.getKey());
            contractNo = (Long) dataPack.get(ProcessDataPackKey.CONTRACT_NO.getKey());
            
            paymentNotificationService = SpringUtil.getBean(PaymentNotificationService.class);
            eventPublisher = SpringUtil.getApplicationContext();
			paymentEventPublisher = SpringUtil.getBean(PaymentEventPublisher.class);
			
			paymentUtilImpl = SpringUtil.getBean(PaymentUtilImpl.class);
			isFomOperationEnabled = paymentUtilImpl.isFomOperationEnabled(institution);

		}
		
	}
	
	private class FetchBillRecord implements ProcessStep {
		@Override
		public void executeStep() {
            paymentService = SpringUtil.getBean(PaymentService.class);
			paymentDTO = paymentService.getPayment(billId, contractNo);
		}
	}
	
	private class CheckInstitution implements ProcessStep {
		@Override
		public void executeStep() {
			String findSubscriber = null;

			if(institution.getIsReverseAllowed() == Boolean.FALSE) {
				institutionFeatureService = SpringUtil.getBean(InstitutionFeatureService.class);
				String featureValue = institutionFeatureService.getFeatureValue(EnumFeatureCode.SUBSCRIBER_REVERSE_ALLOWED,
						institution.getInstitutionCode(), institution.getProduct().getCode());

				if (featureValue != null) {
					List<String> subscriberReverseAllowedList = Arrays.asList(featureValue.split(","));
					findSubscriber = subscriberReverseAllowedList.stream()
							.filter(str -> str.equals(paymentDTO.getSubscriberNo())).findAny().orElse(null);
				}	
			}

			if (findSubscriber == null && institution.getIsReverseAllowed() == Boolean.FALSE) {
				error = EnumBillResult.REVERSE_PAYMENT_FORBIDDEN;
			}
		}
	}

	private class CheckIsReversible implements ProcessStep {
		@Override
		public void executeStep() {
			channelService = SpringUtil.getBean(ChannelService.class);
			ChannelDTO cancelPaymentChannel = channelService.findChannelByChannelCode(channelCode);

			boolean areChannelsTheSameAdkGroup = channelService.areChannelsTheSameAdkGroup(cancelPaymentChannel,
					channelService.findChannelByChannelCode(paymentDTO.getChannelCode()));

			if (paymentDTO.getChannelCode().equals(channelCode) && !areChannelsTheSameAdkGroup) {
				error = EnumBillResult.BILL_REVERSE_PAYMENT_CHANNEL_DIFFERENT_ERROR;
			}
			

		}
	}
	
	
	private class OnlineCheckIsReversible implements ProcessStep {
		@Override
		public void executeStep() {
			/*
			 * TODO: mevcutta checkIsReversablePayment online olarak kurumlarda call edilen bir yapı  ( odemenin iptal edilip edilemeyeceginin ogrenilmesini saglayan bir yapı)
				     bu yapı sadece IZMIT-SU ve ADAPAZARI-DOGALGAZ icerisinde yer alıyor
					 ve sadece ADAPAZARI-DOGALGAZ icin kurumun bir servisini call etmisiz. Bir kurum icin bu yapıyı desteklemeye gerek var mı?
			 * 
			 */
		}
	}
			
	private class DoAccounting implements ProcessStep {
		@Override
		public void executeStep() {
			
			accountingService = SpringUtil.getBean(AccountingService.class);
			createReverseAccountingResultDTO  = accountingService.doReverseAccounting(prepareCreateReverseAccounting());
			if(!createReverseAccountingResultDTO.isSuccess()){
				error = createReverseAccountingResultDTO.getError();
			}
		}
		
		private CreateReverseAccountingDTO prepareCreateReverseAccounting() {
			CreateReverseAccountingDTO createReverseAccountingDTO = new CreateReverseAccountingDTO();
			createReverseAccountingDTO.setChannelTransactionId(paymentDTO.getChannelTransactionId());
			createReverseAccountingDTO.setContractNo(paymentDTO.getContractNo());
			createReverseAccountingDTO.setPaymentMethodType(paymentDTO.getPaymentMethod());
			if (EnumProvisionType.CARD.equals(paymentDTO.getPaymentMethod().getProvisionType())) {
				createReverseAccountingDTO.setDummyMerchant(paymentDTO.getAccountingSources().equals(EnumAccountingSource.PAYMENT));
			}
			return createReverseAccountingDTO;
			
		}
	}
	
	private class InsertPaymentReverseRecord implements ProcessStep {
		@Override
		public void executeStep() {
			 preparePaymentCancelDTO();
	         paymentCancelDTO = paymentService.insertPaymentCancel(paymentCancelDTO);
		}
		
		private void preparePaymentCancelDTO() {
			paymentService = SpringUtil.getBean(PaymentService.class);

			paymentCancelDTO = new PaymentCancelDTO();
			paymentCancelDTO.setPaymentId(paymentDTO.getId());

			if (EnumProvisionType.CARD.equals(paymentDTO.getPaymentMethod().getProvisionType())) {

				String provisionRequestId = null;
				if(paymentDTO.getAccountingSources().equals(EnumAccountingSource.PAYMENT)) {
					provisionRequestId = CommonUtils.generateCreditCardProvisionRequestId(paymentDTO.getChannelCode(),true);
				}else {
					provisionRequestId = CommonUtils.generateCreditCardProvisionRequestId(paymentDTO.getChannelCode(),false);
				}
				paymentCancelDTO.setReverseProvisionRequestId(provisionRequestId);
			}

			paymentCancelDTO.setCancelDate(LocalDate.now());
			paymentCancelDTO.setBranchCode(branchCode);
			paymentCancelDTO.setChannelCode(channelCode);
			paymentCancelDTO.setChannelSessionId(channelSessionId);
			paymentCancelDTO.setChannelTransactionId(channelTransactionId);
			paymentCancelDTO.setChannelTransactionId(channelSessionId);
			paymentCancelDTO.setCreatedBy(agentCode);

		}
	}
	
	private class UpdateCustomerPaymentLimit implements ProcessStep {
		@Override
		public void executeStep() {
			
			if (!isFomOperationEnabled) { // FOM operations are disabled for this product
				return;
			}

			Integer customerInt = null;
			if (paymentDTO.getCustomerNo() != null) {
				customerInt = paymentDTO.getCustomerNo().intValue();
			}
			
			String identityNoStr = null;
			if (paymentDTO.getIdentityNo() != null) {
				identityNoStr = paymentDTO.getIdentityNo().toString();
			}

			NotifyPaymentLimitationRequest request = new NotifyPaymentLimitationRequest();
			request.setCancel(true);
			request.setChannelCode(channelCode);
			request.setClientNo(customerInt);
			request.setIdentityNo(identityNoStr);
			request.setContractNo(paymentDTO.getContractNo());
			request.setCreatedBy(agentCode);
			request.setProductCode("B0001");
			request.setReferenceNo(paymentDTO.getContractNo().toString());
			request.setTransactionDate(LocalDateTime.now());
			paymentEventPublisher.publishPaymentLimiationNotification(request);			
		}
	}
	
	private class UpdatePaymentRecord implements ProcessStep {
		@Override
		public void executeStep() {
			paymentService.updateStatus(EnumBillStatu.CANCELLED,billId,contractNo);
		}
	}
	
	private class InsertPaymentCancelNotificationRecord implements ProcessStep {
		@Override
		public void executeStep() {
			List<EnumPaymentNotificationType> enumValues = Arrays.asList(EnumPaymentNotificationType.INSTITUTION_CANCEL_NOTIFICATION,EnumPaymentNotificationType.RVRS_PROVISION);
			insertedPaymentNotificationDTOList = createPaymentNotifications(enumValues);
		}

		private List<PaymentNotificationDTO> createPaymentNotifications(List<EnumPaymentNotificationType> notificationTypes) {
			return notificationTypes.stream().map(this::createPaymentNotification).toList();
		}
		private PaymentNotificationDTO createPaymentNotification(EnumPaymentNotificationType notificationType) {
			boolean validationFlag = Boolean.TRUE;
			PaymentNotificationDTO paymentNotificationDTO = new PaymentNotificationDTO();

			if(notificationType.equals(EnumPaymentNotificationType.RVRS_PROVISION)) {
				validationFlag = paymentDTO.getPaymentMethod().getProvisionType().equals(EnumProvisionType.CARD);
			}
			if(validationFlag) {
			paymentNotificationDTO.setPaymentId(paymentDTO.getId());
			paymentNotificationDTO.setNotificationType(notificationType);
			paymentNotificationDTO.setNotificationStatus(EnumPaymentNotificationStatu.WAITING);
			paymentNotificationDTO.setRetryCount(0);
			paymentNotificationDTO.setCreateDate(LocalDate.now());
			paymentNotificationDTO.setCreateTime(LocalDateTime.now());
			paymentNotificationDTO.setCreatedBy(agentCode);

			paymentNotificationDTO = paymentNotificationService
					.insertPaymentNotification(paymentNotificationDTO);
			}
			return paymentNotificationDTO;
		}
	}

	
	private class PublishBillPaymentCancelEvent implements ProcessStep {
		@Override
		public void executeStep() {
			
				PublishPaymentTypeDTO publishPaymentTypeDTO = new PublishPaymentTypeDTO();
				publishPaymentTypeDTO.setInsertedPaymentNotificationDTOList(insertedPaymentNotificationDTOList);
				publishPaymentTypeDTO.setInstitutionDTO(institution);
				publishPaymentTypeDTO.setPaymentDTO(paymentDTO);
				publishPaymentTypeDTO.setPaymentCancelDTO(paymentCancelDTO);
				
				paymentEventPublisher.findPublishPaymentCancelEvent(publishPaymentTypeDTO);

		}

	}
	
	
	private void prepareLogVariable() {
		if (paymentDTO != null) {
			logDTO.setCustomerNo(paymentDTO.getCustomerNo());
			logDTO.setIdentityNo(paymentDTO.getIdentityNo());
			logDTO.setTaxId(paymentDTO.getTaxId());
			logDTO.setSubscriberNo(paymentDTO.getSubscriberNo());			
		}

	}
	
}


class BillPaymentReverseProcessTest {

    @Mock
    private ProvisionServiceImpl provisionService;

    @Mock
    private ChannelService channelService;

    @Mock
    private InstitutionFeatureService institutionFeatureService;

    @Mock
    private AccountingServiceImpl accountingService;

    @Mock
    private InstitutionChannelPymMethodServiceImpl institutionChannelPymMethodService;

    @Mock
    private InstitutionChnnlPymMthdAccServiceImpl institutionChnnlPymMthdAccService;

    @Mock
    private PaymentEventPublisher paymentEventPublisher;
    
    @Mock
    private PaymentNotificationService paymentNotificationService;

    @Mock
    private InstitutionChnnlPymMthdPscServiceImpl institutionChnnlPymMthdPscService;

    @Mock
    private InstitutionAccountingInfoServiceImpl institutionAccountingInfoService;

    @Mock
    private PaymentServiceImpl paymentService;

    @InjectMocks
    private BillPaymentReverseProcess billPaymentReverseProcess;

    private ProvisionDTO provisionDTO;
    private PaymentDTO paymentDTO;
    private InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO;
    private InstitutionChnnlPymMthdAccDTO institutionChnnlPymMthdAccDTO;
    private CreateAccountingResultDTO createAccountingResultDTO;
    @Mock
    private PaymentUtil paymentUtil;

    @Mock
    private PaymentUtilImpl paymentUtilImpl;
    private AccountPaymentMethodDetailDTO paymentMethodDetailDTO;

    private PaymentCancelDTO paymentCancelDTO;


    @Mock
    private ApplicationContext applicationContext;

    


    @Test
     void testExecuteProcess_Success() throws BillException {

        SpringUtil springUtil = new SpringUtil();
        springUtil.setApplicationContext(applicationContext);

        when(getBean(InstitutionChannelPymMethodService.class)).thenReturn(institutionChannelPymMethodService);
        when(getBean(InstitutionChnnlPymMthdAccService.class)).thenReturn(institutionChnnlPymMthdAccService);
        when(getBean(InstitutionAccountingInfoService.class)).thenReturn(institutionAccountingInfoService);
        when(getBean(ProvisionService.class)).thenReturn(provisionService);
        when(getBean(AccountingService.class)).thenReturn(accountingService);
        when(getBean(PaymentService.class)).thenReturn(paymentService);
        when(getBean(PaymentNotificationService
                .class)).thenReturn(paymentNotificationService);
        when(getBean(PaymentEventPublisher.class)).thenReturn(paymentEventPublisher);
        when(getBean(PaymentUtilImpl.class)).thenReturn(paymentUtilImpl);
        when(getBean(ChannelService.class)).thenReturn(channelService);
        when(getBean(InstitutionFeatureService.class)).thenReturn(institutionFeatureService);


        provisionDTO = new ProvisionDTO();
        provisionDTO.setId(1L);
        provisionDTO.setStatus(EnumProvisionStatus.NOT_PAID);
        provisionDTO.setProvisionDate(LocalDate.now());
        provisionDTO.setAmount(BigDecimal.valueOf(100));

        institutionChannelPymMethodDTO = new InstitutionChannelPymMethodDTO();
        institutionChannelPymMethodDTO.setBlockDayStrategyCode(EnumBlockDayStrategyCode.DAILY);
        institutionChannelPymMethodDTO.setIsActive(true);

        institutionChnnlPymMthdAccDTO = new InstitutionChnnlPymMthdAccDTO();
        institutionChnnlPymMthdAccDTO.setIsActive(true);
        institutionChnnlPymMthdAccDTO.setInstitutionAccountNo("4567");

        createAccountingResultDTO = new CreateAccountingResultDTO();
        createAccountingResultDTO.setSuccess(true);

        InstitutionDTO institutionDTO = new InstitutionDTO();
        institutionDTO.setProduct(new ProductDTO());
        billPaymentReverseProcess.setInstitution(institutionDTO);

        InstitutionChannelDTO institutionChannelDTO = new InstitutionChannelDTO();
        institutionChannelDTO.setId(123L);
        institutionChannelDTO.setIsOverPaymentAllowed(true);
        institutionChannelDTO.setIsPartialPaymentAllowed(true);
        billPaymentReverseProcess.setInstitutionChannel(institutionChannelDTO);


        InstitutionChnnlPymMthdPscDTO institutionChnnlPymMthdPscDTO = new InstitutionChnnlPymMthdPscDTO();

        billPaymentReverseProcess.setDataPack(new HashMap<>());
        billPaymentReverseProcess.getDataPack().put(ProcessDataPackKey.BILL_PROVISION_ID.getKey(), 1L);
        billPaymentReverseProcess.getDataPack().put(ProcessDataPackKey.PAYMENT_AMOUNT.getKey(), BigDecimal.valueOf(100));
        billPaymentReverseProcess.getDataPack().put(ProcessDataPackKey.CURRENCY.getKey(), "USD");
        billPaymentReverseProcess.getDataPack().put(ProcessDataPackKey.PAYMENT_METHOD_TYPE.getKey(), EnumPaymentMethod.ACCOUNT);

        paymentMethodDetailDTO = new AccountPaymentMethodDetailDTO();
        paymentMethodDetailDTO.setAccountNo("12345");
        billPaymentReverseProcess.getDataPack().put(ProcessDataPackKey.ACCOUNT_PAYMENT_METHOD_DETAIL.getKey(), paymentMethodDetailDTO);

        ProcessLogDTO processLogDTO = new ProcessLogDTO("processCode");
        billPaymentReverseProcess.setLogDTO(processLogDTO);

        ProcessExecutionOutput processExecutionOutput = new ProcessExecutionOutput() {
        };
        processExecutionOutput.setResult(EnumBillResult.OVER_PAYMENT_NOT_ALLOWED_ERROR);
        billPaymentReverseProcess.setExecutionOutput(processExecutionOutput);

        paymentDTO = new PaymentDTO();
        paymentDTO.setId(123L);
        paymentDTO.setPaymentMethod(EnumPaymentMethod.ACCOUNT);
        billPaymentReverseProcess.setStepHandler(new AbstractProcess() {
            public AbstractProcess.ProcessStepHandler ProcessStepHandler = new ProcessStepHandler();

            @Override
            public void executeProcess() throws BillException {
                System.out.println();
            }

            @Override
            protected void prepareExecutionOutput() {
                System.out.println();
            }
        }.ProcessStepHandler);

        InstitutionDebtTypeDTO institutionDebtTypeDTO = new InstitutionDebtTypeDTO();
        institutionDebtTypeDTO.setId(123L);
        billPaymentReverseProcess.setInstitutionDebtType(institutionDebtTypeDTO);
        InstitutionAccountingInfoDTO institutionAccountingInfoDTO = new InstitutionAccountingInfoDTO();
        when(provisionService.getProvisionRecord(1L)).thenReturn(provisionDTO);
        when(institutionChannelPymMethodService.getInstitutionPymMethodByChannelId(any(), any())).thenReturn(institutionChannelPymMethodDTO);
        when(institutionChnnlPymMthdAccService.getInstitutionChnnlPymMthdAcc(any(), any())).thenReturn(institutionChnnlPymMthdAccDTO);
        when(institutionChnnlPymMthdPscService.getInstitutionChnnlPymMthdPscByChannelMethod(any())).thenReturn(institutionChnnlPymMthdPscDTO);
        when(accountingService.doAccounting(any(CreateAccountingDTO.class))).thenReturn(createAccountingResultDTO);
        when(paymentService.insertPayment(any())).thenReturn(paymentDTO);


        billPaymentReverseProcess.executeProcess();

        verify(provisionService).getProvisionRecord(1L);
    }
}
