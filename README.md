 private static Map<EnumProvisionType, ProvisionService> provisionServiceMap = null;

    @Autowired
    private ProvisionFactory(List<ProvisionService> provisionServices){
        provisionServiceMap = provisionServices.stream().collect(Collectors.toUnmodifiableMap(ProvisionService::getProvisionType, Function.identity()));
    }

    public ProvisionService getProvisionService(EnumProvisionType provisionType) {
        return   Optional.ofNullable(provisionServiceMap.get(provisionType)).orElseThrow(IllegalArgumentException::new);
    }
