@Service
@RequiredArgsConstructor
public class ProcessServiceImpl implements ProcessService {

	private final ProcessChannelService processChannelService;
	private final InstitutionService institutionService;
	private final InstitutionDebtTypeService institutionDebtTypeService;	


@Override
	@Cacheable(value = "getProcessChannelForProcess", cacheManager = CacheConstants.CACHE_MANAGER)
	public ProcessChannelDTO getProcessChannel(String code, String channelCode) {
		if (StringUtils.isEmpty(code) || StringUtils.isEmpty(channelCode)) {
			return null;
		}

		return processChannelService.findProcessChannel(code, channelCode);
	}
	@Override
	@Cacheable(value = "getInstitutionDebtTypeForProcess", cacheManager = CacheConstants.CACHE_MANAGER)
	public InstitutionDebtTypeDTO getInstitutionDebtTypeForProcess(String productCode, String institutionCode,Long institutionDebtTypeId) {
		if(institutionDebtTypeId != null) {
			return institutionDebtTypeService.getDebtType(institutionDebtTypeId);
		}
		if(!StringUtils.isAnyEmpty(productCode,institutionCode)){
			return institutionDebtTypeService.getDefaultDebtType(productCode,institutionCode);
		}
		return null;
	}
