  private static Map<EnumProvisionType, ProvisionService> provisionServiceMap = null;

    @Autowired
    private ProvisionFactory(List<ProvisionService> provisionServices){
        provisionServiceMap = provisionServices.stream().collect(Collectors.toUnmodifiableMap(ProvisionService::getProvisionType, Function.identity()));
    }

bu metodun testini yaz
