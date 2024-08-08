@Tag(name = "ADK Bill Payment")
@RequestMapping("/adkBillPayment")
@RequiredArgsConstructor
@RestController
public class PaymentNotificationController {

    private final PaymentNotificationService paymentNotificationService;
    
    @Schema(description = "Send credit card provision ack")
    @PostMapping(value = "/send-credit-card-provision-ack")
    public ResponseEntity<Void> sendCreditCardProvisionAck(
            @RequestBody GetCreditCardProvisionAckRequest request
           ) throws BusinessException {

        paymentNotificationService.sendAckForCreditCardProvision(request.getPaymentDTO().getId(),request.getPaymentNotificationId(), request.getIsBatch());

        return new ResponseEntity<>(HttpStatus.OK);

    }

}
