@Service
@RequiredArgsConstructor
public class InstitutionBarcodeServiceImpl implements InstitutionBarcodeService {

	private final InstitutionFeatureService institutionFeatureService;

	@Override
	public GetSubscriberNoWithBarcodeResponse getSubscriberNoWithBarcode(GetSubscriberNoWithBarcodeRequest request) {

		GetSubscriberNoWithBarcodeResponse response = new GetSubscriberNoWithBarcodeResponse();
		String subscriberNo = "";		

		String featureValue = institutionFeatureService.getFeatureValue(EnumFeatureCode.BARCODE_PAYMENT_FOR_INSTITUTION,
				request.getInstitutionCode(), request.getProductCode());
		Boolean barcodeFlag = ObjectUtil.dbValueToBoolean(Integer.valueOf(featureValue));
		if (barcodeFlag) {
			String barcodeParserRule = institutionFeatureService.getFeatureValue(
					EnumFeatureCode.BARCODE_PARSER_RULE_FOR_INSTITUTION, request.getInstitutionCode(),
					request.getProductCode());
			Pattern pattern = Pattern.compile(barcodeParserRule);

			List<String> namedGroups = getNamedGroup(barcodeParserRule);
			Matcher matcher = pattern.matcher(request.getBarcode());
			if (matcher.find()) {
				for (String namedGroup : namedGroups) {

					subscriberNo = subscriberNo + matcher.group(namedGroup);					
				}

			}	
			
			response.setSubscriberNo(subscriberNo);	
			
		}
			
		// Simdilik hata kodu default setlendi
		response.setReturnCode("0");
		response.setReturnMessage("Success");
		return response;
				
	}

	private static List<String> getNamedGroup(String regex) {
		List<String> namedGroups = new ArrayList<String>();

		Matcher m = Pattern.compile("\\(\\?<([a-zA-Z][a-zA-Z0-9]*)>").matcher(regex);
		while (m.find()) {
			namedGroups.add(m.group(1));
		}
		Collections.sort(namedGroups);
		return namedGroups;
	}
}
