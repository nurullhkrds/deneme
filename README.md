@Service
@RequiredArgsConstructor
public class AdminChannelServiceImpl implements AdminChannelService {

	private final ChannelRepository channelRepository;
	private final AdminChannelMapper mapper;

	@Override
	public ChannelDTO findChannelByChannelCode(String channelCode) {
		if(StringUtils.isEmpty(channelCode)) {
			return null;
		}	
		
		return mapper.toDto(channelRepository.findByCode(channelCode));
	}

	
}
