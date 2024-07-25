@Service
@RequiredArgsConstructor
public class PaymentFacadeImpl implements  PaymentFacade{

    private final PaymentService paymentService;
    private final SubscriberService subscriberService;
    private final InstitutionFeatureService institutionFeatureService;
    private final BillPaymentRestFacade billPaymentRestFacade;


    @Override
    public QueryBillsResponse queryBills(QueryBillsRequest queryBillsRequest) throws MicroException {
        if(isMicroInstitution(queryBillsRequest.getProductCode(),queryBillsRequest.getInstitutionCode())){
            return paymentService.queryBills(queryBillsRequest);
        }
        return billPaymentRestFacade.queryBills(queryBillsRequest);
    }

    @Override
    public DoBillPaymentResponse doBillPayment(DoBillPaymentRequest doBillPaymentRequest) throws MicroException {
        if(isMicroInstitution(doBillPaymentRequest.getProductCode(),doBillPaymentRequest.getInstitutionCode())){
            return paymentService.doBillPayment(doBillPaymentRequest);
        }
        return billPaymentRestFacade.doBillPayment(doBillPaymentRequest);
    }

    @Override
    public CancelBillPaymentResponse cancelBillPayment(CancelBillPaymentRequest cancelBillPaymentRequest) throws MicroException {
        if(isMicroInstitution(cancelBillPaymentRequest.getProductCode(),cancelBillPaymentRequest.getInstitutionCode())){
            return paymentService.cancelBillPayment(cancelBillPaymentRequest);
        }
        return billPaymentRestFacade.cancelBillPayment(cancelBillPaymentRequest);
    }

    @Override
    public GetBillPaymentExpenseResponseDTO getBillPaymentExpense(GetBillPaymentExpenseRequestDTO subscriberValidationRequestDTO) throws MicroException {
        if(isMicroInstitution(subscriberValidationRequestDTO.getProductCode(),subscriberValidationRequestDTO.getInstitutionCode())){
            return subscriberService.getBillPaymentExpense(subscriberValidationRequestDTO);
        }
        return billPaymentRestFacade.getBillPaymentExpense(subscriberValidationRequestDTO);
    }

    private boolean isMicroInstitution(String product,String institution ){
        String featureValue = institutionFeatureService.getFeatureValue(EnumFeatureCode.MICRO_INSTITUTION,institution,product);
        if(featureValue != null && featureValue.equals(EnumYesNo.YES.getValue())){
            return  true;
        }
        return false;
    }
}
