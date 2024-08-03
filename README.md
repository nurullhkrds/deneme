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
