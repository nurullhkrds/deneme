@Service
@RequiredArgsConstructor
public class ProvisionServiceImpl implements ProvisionService {
	
	private final ProvisionRepository provisionRepository;
	private final ProvisionMapper provisionMapper;
	
	@Override
	public ProvisionDTO getProvisionRecord(Long id) {
		Optional<Provision> entity = provisionRepository.findById(id);
		if (entity.isPresent()){
			return provisionMapper.toDTO(entity.get());
		}
		return null;
	}

	@Override
	@Transactional
	public List<ProvisionDTO>  createProvisions(List<ProvisionDTO> provisionDTOList) {
		List<Provision> provisionList = provisionRepository.saveAll(provisionMapper.toEntityList(provisionDTOList));
		return provisionMapper.toDTOList(provisionList);
	}

	@Override
	@Transactional
	public void updateCommissionData(String commissionData, Long id) {
		provisionRepository.updateCommissionData(commissionData, id);
		
	}

	@Override
	@Transactional
	public void invalidateNotPaidProvisions(Long institutionDebtTypeId, String subscriberNo) {
		Optional<List<Provision>>  opProvisionList = provisionRepository.findBynDebtTypeIdAndSubscriberNoWitLock(institutionDebtTypeId,subscriberNo);
		if(opProvisionList.isPresent()){
			opProvisionList.get().forEach(provision -> provision.setStatus(EnumProvisionStatus.CANCELLED));
			//TODO:EntityManager Refresh degerlendir
			provisionRepository.saveAll(opProvisionList.get());
		}

	}

	@Override
	public void updateProvision(ProvisionDTO provisionDTO) {
		Provision entity = provisionMapper.toEntity(provisionDTO);
		provisionRepository.save(entity);
	}

}
