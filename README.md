public interface ChannelService {

	ChannelDTO findChannelByChannelCode(String channelCode);

}
@Service
@RequiredArgsConstructor
public class ChannelServiceImpl implements ChannelService {

	private final ChannelRepository channelRepository;
	private final ChannelMapper mapper;

	@Override
	public ChannelDTO findChannelByChannelCode(String channelCode) {
		if(StringUtils.isEmpty(channelCode)) {
			return null;
		}	
		
		return mapper.toDto(channelRepository.findByCode(channelCode));
	}

	
}
@Service
public class InstitutionChannelServiceImpl implements InstitutionChannelService {

    private final InstitutionChannelRepository institutionChannelRepository;
    private final InstitutionChannelMapper institutionChannelMapper;

    private final InstitutionDebtTypeService institutionDebtTypeService;
    private final ChannelService channelService;

    public InstitutionChannelServiceImpl(InstitutionChannelRepository institutionChannelRepository, InstitutionChannelMapper institutionChannelMapper, InstitutionDebtTypeService institutionDebtTypeService, ChannelService channelService) {
        this.institutionChannelRepository = institutionChannelRepository;
        this.institutionChannelMapper = institutionChannelMapper;
        this.institutionDebtTypeService = institutionDebtTypeService;
        this.channelService = channelService;
    }
