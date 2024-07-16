package com.ykb.payments.bill.transaction.payment.event;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.context.event.EventListener;
import org.springframework.scheduling.annotation.Async;
import org.springframework.stereotype.Service;
import org.springframework.transaction.event.TransactionPhase;
import org.springframework.transaction.event.TransactionalEventListener;

import com.ykb.payments.bill.common.util.SpringUtil;
import com.ykb.payments.bill.transaction.external.limitation.model.NotifyInquiryLimitationRequest;
import com.ykb.payments.bill.transaction.external.limitation.model.NotifyPaymentLimitationRequest;
import com.ykb.payments.bill.transaction.external.limitation.service.LimitationService;
import com.ykb.payments.bill.transaction.payment.model.BillPaymentCancelEvent;
import com.ykb.payments.bill.transaction.payment.model.BillPaymentEvent;
import com.ykb.payments.bill.transaction.payment.model.CreditCardProvisionACKEventDTO;
import com.ykb.payments.bill.transaction.payment.model.CreditCardProvisionReverseEventDTO;
import com.ykb.payments.bill.transaction.payment.model.PaymentCancelNotificationEvent;
import com.ykb.payments.bill.transaction.payment.model.PaymentNotificationEvent;
import com.ykb.payments.bill.transaction.payment.producer.PaymentNotificationEventProducer;

import lombok.RequiredArgsConstructor;

@RequiredArgsConstructor
@Service
public class PaymentEventListener {

    private final Logger logger = LoggerFactory.getLogger(PaymentEventListener.class);
    
    private final PaymentNotificationEventProducer paymentNotificationEventProducer;
    private LimitationService limitationService;
    
    @Async
    @EventListener(BillPaymentEvent.class)
 //   @TransactionalEventListener(phase = TransactionPhase.AFTER_COMMIT)
    public void onPaymentCreatedNotificationEvent(BillPaymentEvent billPaymentEvent) {    	
		PaymentNotificationEvent paymentNotificationEvent = new PaymentNotificationEvent();
		
		paymentNotificationEvent.setCreatedBy(billPaymentEvent.getPaymentDTO().getCreatedBy());
		paymentNotificationEvent.setBranchCode(billPaymentEvent.getPaymentDTO().getBranchCode());
		paymentNotificationEvent.setChannelCode(billPaymentEvent.getPaymentDTO().getChannelCode());
		paymentNotificationEvent.setChannelSessionId(billPaymentEvent.getPaymentDTO().getChannelSessionId());
		paymentNotificationEvent.setChannelTransactionId(billPaymentEvent.getPaymentDTO().getChannelTransactionId());
		paymentNotificationEvent.setInstitutionId(billPaymentEvent.getPaymentDTO().getInstitutionId());
		paymentNotificationEvent.setPaymentNotificationId(billPaymentEvent.getPaymentNotificationId());
		paymentNotificationEvent.setProductCode(billPaymentEvent.getInstitutionDTO().getProduct().getCode());
		paymentNotificationEvent.setInstitutionCode(billPaymentEvent.getInstitutionDTO().getInstitutionCode());
		
		if (paymentNotificationEventProducer == null) {
            logger.error("[onPaymentCreatedNotificationEvent] -> paymentNotificationEventProducer is null because rabbit is disabled!!!");
            return;
        }
		
        paymentNotificationEventProducer.sendPaymentNotificationEvent(paymentNotificationEvent);
    }
    
	@Async
	@EventListener(BillPaymentCancelEvent.class)
	public void onPaymentCancelCreatedNotificationEvent(BillPaymentCancelEvent event) {
		PaymentCancelNotificationEvent notifyCancelPaymentEvent = new PaymentCancelNotificationEvent();
		notifyCancelPaymentEvent.setCreatedBy(event.getCancelRecord().getCreatedBy());
		notifyCancelPaymentEvent.setBranchCode(event.getCancelRecord().getBranchCode());
		notifyCancelPaymentEvent.setChannelCode(event.getCancelRecord().getChannelCode());
		notifyCancelPaymentEvent.setChannelSessionId(event.getCancelRecord().getChannelSessionId());
		notifyCancelPaymentEvent.setChannelTransactionId(event.getCancelRecord().getChannelTransactionId());
		notifyCancelPaymentEvent.setInstitutionId(event.getInstitution().getId());
		notifyCancelPaymentEvent.setPaymentNotificationId(event.getPaymentNotificationId());
		notifyCancelPaymentEvent.setProductCode(event.getInstitution().getProduct().getCode());
		notifyCancelPaymentEvent.setInstitutionCode(event.getInstitution().getInstitutionCode());
		
		if (paymentNotificationEventProducer == null) {
            logger.error("[onPaymentCreatedNotificationEvent] -> paymentNotificationEventProducer is null because rabbit is disabled!!!");
            return;
        }
		
		paymentNotificationEventProducer.sendPaymentCancelNotificationEvent(notifyCancelPaymentEvent);

	}
	
    @Async
    @EventListener(CreditCardProvisionACKEventDTO.class)
    public void onCreditCardProvisionACKEvent(CreditCardProvisionACKEventDTO creditCardProvisionACKEventDTO) {
        if (paymentNotificationEventProducer == null) {
            logger.error("[onCreditCardProvisionACKEvent] -> paymentNotificationEventProducer is null because rabbit is disabled!!!");
            return;
        }
        paymentNotificationEventProducer.sendCreditCardProvisionACKEvent(creditCardProvisionACKEventDTO);
    }
    
    @Async
    @EventListener(CreditCardProvisionReverseEventDTO.class)
    public void onCreditCardProvisionReverseEvent(CreditCardProvisionReverseEventDTO creditCardProvisionReverseEventDTO) {
        if (paymentNotificationEventProducer == null) {
            logger.error("[onCreditCardProvisionReverseEvent] -> paymentNotificationEventProducer is null because rabbit is disabled!!!");
            return;
        }
        paymentNotificationEventProducer.sendCreditCardProvisionReverseEvent(creditCardProvisionReverseEventDTO);
    }
    
    @Async
    @EventListener(NotifyPaymentLimitationRequest.class)
	//@TransactionalEventListener(phase = TransactionPhase.AFTER_COMMIT)
	public void onNotifyPaymentLimitation(NotifyPaymentLimitationRequest request) {
    	limitationService = SpringUtil.getBean(LimitationService.class);    	
    	limitationService.notifyPaymentLimitation(request);
	}

    @Async
    @EventListener(NotifyInquiryLimitationRequest.class)	
	public void onNotifyPaymentLimitation(NotifyInquiryLimitationRequest request) {
    	limitationService = SpringUtil.getBean(LimitationService.class);
		limitationService.notifyInquiryLimitation(request);
	}
    
    
}









package com.ykb.payments.bill.transaction.payment.event;

import java.util.HashMap;
import java.util.Map;
import java.util.Optional;
import java.util.function.BiConsumer;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationEventPublisher;
import org.springframework.stereotype.Component;

import com.ykb.payments.bill.transaction.external.limitation.model.NotifyInquiryLimitationRequest;
import com.ykb.payments.bill.transaction.external.limitation.model.NotifyPaymentLimitationRequest;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionDTO;
import com.ykb.payments.bill.transaction.institution.enums.EnumProvisionType;
import com.ykb.payments.bill.transaction.payment.dto.PaymentCancelDTO;
import com.ykb.payments.bill.transaction.payment.dto.PaymentDTO;
import com.ykb.payments.bill.transaction.payment.dto.PaymentNotificationDTO;
import com.ykb.payments.bill.transaction.payment.enums.EnumPaymentNotificationType;
import com.ykb.payments.bill.transaction.payment.model.BillPaymentCancelEvent;
import com.ykb.payments.bill.transaction.payment.model.BillPaymentEvent;
import com.ykb.payments.bill.transaction.payment.model.CreditCardProvisionACKEventDTO;
import com.ykb.payments.bill.transaction.payment.model.CreditCardProvisionReverseEventDTO;
import com.ykb.payments.bill.transaction.payment.model.PublishPaymentTypeDTO;



@Component
public class PaymentEventPublisher {

    private ApplicationEventPublisher eventPublisher;
        
    @Autowired
    public PaymentEventPublisher(ApplicationEventPublisher eventPublisher) {
        this.eventPublisher = eventPublisher;
    }
    
	public void findPublishPaymentEvent(PublishPaymentTypeDTO publishPaymentTypeDTO) {
		Optional<PaymentNotificationDTO> findPaymentNotificationEvent = publishPaymentTypeDTO
				.getInsertedPaymentNotificationDTOList().stream()
				.filter(paymentNotificationType -> paymentNotificationType.getNotificationType().getValue()
						.equals(EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION.getValue()))
				.findFirst();

		if (findPaymentNotificationEvent.isPresent()) {
			publishPaymentNotificationEvent(publishPaymentTypeDTO.getPaymentDTO(),
					publishPaymentTypeDTO.getInstitutionDTO(), findPaymentNotificationEvent.get().getId());

		}

		if (EnumProvisionType.CARD
				.equals(publishPaymentTypeDTO.getPaymentDTO().getPaymentMethod().getProvisionType())) {
			Optional<PaymentNotificationDTO> findProvisionACKEvent = publishPaymentTypeDTO
					.getInsertedPaymentNotificationDTOList().stream()
					.filter(paymentNotificationType -> paymentNotificationType.getNotificationType().getValue()
							.equals(EnumPaymentNotificationType.CRD_PRVSN_ACK.getValue()))
					.findFirst();

			if (findProvisionACKEvent.isPresent()) {
				publishCreditCardProvisionACKEvent(publishPaymentTypeDTO.getPaymentDTO().getId(),
						findProvisionACKEvent.get().getId());
			}

		}
	}
	
	public void findPublishPaymentEventV2(PublishPaymentTypeDTO publishPaymentTypeDTO) {
		Map<EnumPaymentNotificationType, BiConsumer<PublishPaymentTypeDTO, PaymentNotificationDTO>> consumerMap = new HashMap<>();
		consumerMap.put(EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION, this::publishPaymentNotificationEventV2);
		consumerMap.put(EnumPaymentNotificationType.CRD_PRVSN_ACK, this::publishCreditCardProvisionACKEventV2);

		publishPaymentTypeDTO.getInsertedPaymentNotificationDTOList()
				.forEach(each -> consumerMap.get(each.getNotificationType()).accept(publishPaymentTypeDTO, each));

	}
	
    public void publishPaymentNotificationEventV2(PublishPaymentTypeDTO publishPaymentTypeDTO, PaymentNotificationDTO notification) {
    	BillPaymentEvent billPaymentEvent = new BillPaymentEvent();
		billPaymentEvent.setPaymentDTO(publishPaymentTypeDTO.getPaymentDTO());
		billPaymentEvent.setInstitutionDTO(publishPaymentTypeDTO.getInstitutionDTO());
		billPaymentEvent.setPaymentNotificationId(notification.getId());
		eventPublisher.publishEvent(billPaymentEvent);
    }
    
    public void publishCreditCardProvisionACKEventV2(PublishPaymentTypeDTO publishPaymentTypeDTO, PaymentNotificationDTO notification) {
        CreditCardProvisionACKEventDTO creditCardProvisionACKEventDTO = new CreditCardProvisionACKEventDTO();
        creditCardProvisionACKEventDTO.setPaymentId(publishPaymentTypeDTO.getPaymentDTO().getId());
        creditCardProvisionACKEventDTO.setPaymentNotificationId(notification.getId());
        eventPublisher.publishEvent(creditCardProvisionACKEventDTO);
    }
	
	
	public void findPublishPaymentCancelEvent(PublishPaymentTypeDTO publishPaymentTypeDTO) {
		Optional<PaymentNotificationDTO> findPaymentCancelNotificationEvent = publishPaymentTypeDTO
				.getInsertedPaymentNotificationDTOList().stream()
				.filter(paymentNotificationType -> paymentNotificationType.getNotificationType().getValue()
						.equals(EnumPaymentNotificationType.INSTITUTION_CANCEL_NOTIFICATION.getValue()))
				.findFirst();

		if (findPaymentCancelNotificationEvent.isPresent()) {
			publishPaymentCancelNotificationEvent(publishPaymentTypeDTO.getPaymentDTO(),
					publishPaymentTypeDTO.getPaymentCancelDTO(), publishPaymentTypeDTO.getInstitutionDTO(),
					findPaymentCancelNotificationEvent.get().getId());

		}

		if (EnumProvisionType.CARD
				.equals(publishPaymentTypeDTO.getPaymentDTO().getPaymentMethod().getProvisionType())) {
			Optional<PaymentNotificationDTO> findProvisionReverseEvent = publishPaymentTypeDTO
					.getInsertedPaymentNotificationDTOList().stream()
					.filter(paymentNotificationType -> paymentNotificationType.getNotificationType().getValue()
							.equals(EnumPaymentNotificationType.RVRS_PROVISION.getValue()))
					.findFirst();

			if (findProvisionReverseEvent.isPresent()) {
				publishCreditCardProvisionReverseEvent(publishPaymentTypeDTO.getPaymentDTO(),publishPaymentTypeDTO.getPaymentCancelDTO(),
						findProvisionReverseEvent.get().getId());
			}

		}
	}


    public void publishPaymentNotificationEvent(PaymentDTO paymentDTO,
    		InstitutionDTO institutionDTO,Long paymentNotificationID) {
    	BillPaymentEvent billPaymentEvent = new BillPaymentEvent();
		billPaymentEvent.setPaymentDTO(paymentDTO);
		billPaymentEvent.setInstitutionDTO(institutionDTO);
		billPaymentEvent.setPaymentNotificationId(paymentNotificationID);
		eventPublisher.publishEvent(billPaymentEvent);
    }

	public void publishPaymentCancelNotificationEvent(PaymentDTO paymentDTO, PaymentCancelDTO paymentCancelDTO,InstitutionDTO institutionDTO,
			Long paymentNotificationID) {
		BillPaymentCancelEvent billPaymentCancelEvent = new BillPaymentCancelEvent();
		billPaymentCancelEvent.setCancelledPayment(paymentDTO);
		billPaymentCancelEvent.setCancelRecord(paymentCancelDTO);
		billPaymentCancelEvent.setInstitution(institutionDTO);
		billPaymentCancelEvent.setPaymentNotificationId(paymentNotificationID);

		eventPublisher.publishEvent(billPaymentCancelEvent);
	}
    
    public void publishCreditCardProvisionACKEvent(Long paymentId,Long paymentNotificationID) {
        CreditCardProvisionACKEventDTO creditCardProvisionACKEventDTO = new CreditCardProvisionACKEventDTO();
        creditCardProvisionACKEventDTO.setPaymentId(paymentId);
        creditCardProvisionACKEventDTO.setPaymentNotificationId(paymentNotificationID);
        eventPublisher.publishEvent(creditCardProvisionACKEventDTO);
    }
    
    public void publishCreditCardProvisionReverseEvent(PaymentDTO paymentDTO,PaymentCancelDTO paymentCancelDTO, Long paymentNotificationID) {
    	CreditCardProvisionReverseEventDTO creditCardProvisionReverseEventDTO = new CreditCardProvisionReverseEventDTO(); 
    	creditCardProvisionReverseEventDTO.setPaymentId(paymentDTO.getId());
    	creditCardProvisionReverseEventDTO.setPaymentCancelId(paymentCancelDTO.getId());
    	creditCardProvisionReverseEventDTO.setPaymentNotificationId(paymentNotificationID);
        eventPublisher.publishEvent(creditCardProvisionReverseEventDTO);
        
    }
    
    public void publishPaymentLimiationNotification(NotifyPaymentLimitationRequest notifyPaymentLimitationRequest) {    	
        eventPublisher.publishEvent(notifyPaymentLimitationRequest);        
    }    
    
    public void publishInquiryLimiationNotification(NotifyInquiryLimitationRequest notifyInquiryLimitationRequest) {    	
        eventPublisher.publishEvent(notifyInquiryLimitationRequest);
        
    }
    
    
}













spring:
  application:
    name: PAYMENTS.BILL.bill-transaction
  cloud:
    services:
      registrationMethod: direct
    discovery:
      client:
        simple:
          instances:
            -COREBANKING.COMMISSION.calculation[0].uri: url
            -COREBANKING.ACCOUNT.account[0].uri:url
            -PAYMENTS.BILL.bill-adapter[0].uri: url
            -COREBANKING.ACCOUNT.provision-main[0].uri: url
            -CARDPAYMENTS.SWT.swt-switch-integration[0].uri: url
            -PAYMENTS.COMMON.limitation-service[0].uri: url
  datasource:
    username: BILL
    password: sssss
    url: jdbc:oracle:thin:@(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=oprkbarcdbt.sys.yapikredi.com.tr)(PORT=1818))(CONNECT_DATA=(SERVER=dedicated)(SERVICE_NAME=SRVTEST_NYSU)))
    driverClassName: oracle.jdbc.OracleDriver
    hikari:
      maximum-pool-size: 10
      minimum-idle: 1
      data-source-properties:
        oracle.jdbc.ReadTimeout: 30000
        oracle.net.READ_TIMEOUT: 30000
        oracle.net.CONNECT_TIMEOUT: 30000
        "[v$session.program]": ${spring.application.name}
  jpa:
    show-sql: true
    hibernate:
      ddl-auto: none #none || update || create
feign:
  client:
    config:
      default:
        connectTimeout: 5000
        readTimeout: 30000
        loggerLevel: basic
  hystrix:
    enabled: false
hystrix:
  command:
    default:
      execution:
        timeout:
          enabled: false
logging:
  level:
    com:
      netflix:
        discovery: OFF
    org:
      springframework:
        security: ERROR
        web: ERROR
springdoc:
  api-docs:
    path: /actuator/api-docs
management:
  endpoints:
    web:
      exposure:
        include: '*'
cache:
  redis:
    serviceName: PAYMENTS.BILL-TST.Redis
    institutionFeatureValue:
      ttl: 12
    institutionFeatureList:
      ttl: 12
    getProcessChannelForProcess:
      ttl: 12
    getInstitutionForProcess:
      ttl: 12
    getInstitutionChannelForProcess:
      ttl: 12
    getInstitutionProcess:
      ttl: 12
    getInstitutionChannelProcess:
      ttl: 12
    getInstitutionDebtTypeForProcess:
      ttl: 12
    getInstitutionById:
      ttl: 12
    institutionUserInterfaceList:
      ttl: 12
    findChannelByChannelCode:
      ttl: 12
## pcf env redis configuration
runtime:
  platform: local
## RabbitMQ Configuration
rabbitmq:
  enabled: true
  services:
    billtransaction-rabbitmq:
      name: PAYMENTS.BILL-TST.RabbitMQ
      enabled: false
      consumers:
        paymentNotificationEvent:
          minConcurrentConsumers: 2
          maxConcurrentConsumers: 4
          prefetchCount: 10
      producers:
        paymentNotificationEvent:
          exchangeName: billtransaction-direct-exchange
          routingKey: payment-notification-event
      queues:
        paymentNotificationEvent:
          declare: true
          name: payment-notification-queue
          durable: true
          routingKey: payment-notification-event
          arguments:
            x-message-ttl: 300000
          exchange:
            name: billtransaction-direct-exchange
            durable: true
            type: direct
external:
  billPaymentRestFacade:
    address: url
    service:
      readTimeout: 30000
      connectTimeout: 30000




















