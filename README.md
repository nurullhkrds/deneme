@Component
public class PaymentUtilImpl  implements  PaymentUtil {

	InstitutionFeatureService institutionFeatureService; 
	
	public PaymentUtilImpl(InstitutionFeatureService institutionFeatureService) {
		this.institutionFeatureService = institutionFeatureService;
	}
	
	@Override
	public boolean isFomOperationEnabled(InstitutionDTO institution) {
		boolean isFomOperationEnabled = false;
		if(Boolean.TRUE.equals(institution.getProduct().getFomCheckFlag() )) {
			isFomOperationEnabled = true;
		}else {
			isFomOperationEnabled = isFomCheckEnabledFeature(institution.getProduct().getCode(), institution.getInstitutionCode());				
		}
		
		return isFomOperationEnabled;
	}
	
	private boolean isFomCheckEnabledFeature(String product,String institution ){
		String featureValue = institutionFeatureService.getFeatureValue(EnumFeatureCode.FOM_CHECK_ENABLED,institution,product);
        if(featureValue != null && featureValue.equals(EnumYesNo.YES.getValue())){
            return  true;
        }
        return false;
    }
	
	
	
}
