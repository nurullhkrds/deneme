@Component
public class UpdatableBaseEntityListener {

	@Autowired
	private RequestContext requestContext;
	
	@PreUpdate
	public void preUpdate(UpdatableBaseEntity updatableBaseEntity) {
		updatableBaseEntity.setUpdateDate(LocalDateTime.now());

		if (StringUtils.isEmpty(updatableBaseEntity.getUpdatedBy())) {
			updatableBaseEntity.setUpdatedBy(StringUtils.isEmpty(requestContext.getAgentCode()) ? "SYSTEM"
					: requestContext.getAgentCode());
		}
	}
}
