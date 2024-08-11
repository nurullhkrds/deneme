@Service
@RequiredArgsConstructor
public class ProcessServiceImpl implements ProcessService {

	private final ProcessChannelService processChannelService;
	private final InstitutionService institutionService;
	private final InstitutionDebtTypeService institutionDebtTypeService;

	@Override
	@Cacheable(value = "getInstitutionForProcess", cacheManager = CacheConstants.CACHE_MANAGER)
	public InstitutionDTO getInstitutionForProcess(String productCode, String institutionCode) {
		if (StringUtils.isAnyEmpty(productCode, institutionCode)) {
			return null;
		}

		return institutionService.getInstitution(productCode, institutionCode);
	}

	@Override
	@Cacheable(value = "getInstitutionChannelForProcess", cacheManager = CacheConstants.CACHE_MANAGER)
	public InstitutionChannelDTO getInstitutionChannelForProcess(Long institutionDebtTypeId, String channelCode) {
		if (institutionDebtTypeId == null || StringUtils.isEmpty(channelCode)) {
			return null;
		}

		return institutionService.getInstitutionChannel(institutionDebtTypeId, channelCode);
	}

	@Override
	@Cacheable(value = "getProcessChannelForProcess", cacheManager = CacheConstants.CACHE_MANAGER)
	public ProcessChannelDTO getProcessChannel(String code, String channelCode) {
		if (StringUtils.isEmpty(code) || StringUtils.isEmpty(channelCode)) {
			return null;
		}

		return processChannelService.findProcessChannel(code, channelCode);
	}

	@Override
	@Cacheable(value = "getInstitutionProcess", cacheManager = CacheConstants.CACHE_MANAGER)
	public InstitutionProcessDTO getInstitutionProcess(String productCode, String institutionCode, String code) {
		if (StringUtils.isAnyEmpty(productCode, institutionCode, code)) {
			return null;
		}

		return institutionService.getInstitutionProcess(productCode, institutionCode, code);
	}

	@Override
	@Cacheable(value = "getInstitutionChannelProcess", cacheManager = CacheConstants.CACHE_MANAGER)
	public InstitutionChannelProcessDTO getInstitutionChannelProcess(Long institutionDebtTypeId,
			String processCode, String channelCode) {
		if (institutionDebtTypeId == null || StringUtils.isAnyEmpty(processCode, channelCode)) {
			return null;
		}

		return institutionService.getInstitutionChannelProcess(institutionDebtTypeId, processCode, channelCode);
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
}
