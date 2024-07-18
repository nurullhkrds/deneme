@Component
public class ReverseProvisionFactory {
	
	   private static Map<EnumProvisionType, ReverseProvisionService> reverseProvisionServiceMap = null;

	    @Autowired
	    private ReverseProvisionFactory(List<ReverseProvisionService> reverseProvisionServices){
	        reverseProvisionServiceMap = reverseProvisionServices.stream().collect(Collectors.toUnmodifiableMap(ReverseProvisionService::getProvisionType, Function.identity()));
	    }

	    public ReverseProvisionService getReverseProvisionService(EnumProvisionType provisionType) {
	        return   Optional.ofNullable(reverseProvisionServiceMap.get(provisionType)).orElseThrow(IllegalArgumentException::new);
	    }
