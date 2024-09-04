@Component
public class CreatableBaseEntityListener {

	@Autowired
	private RequestContext requestContext;

	@PrePersist
	public void prePersist(CreatableBaseEntity creatableBaseEntity) {
		if (creatableBaseEntity.getCreateDate() == null) {
			creatableBaseEntity.setCreateDate(LocalDateTime.now());
		}

		if (StringUtils.isEmpty(creatableBaseEntity.getCreatedBy())) {
			creatableBaseEntity.setCreatedBy(StringUtils.isEmpty(requestContext.getAgentCode()) ? "SYSTEM"
					: requestContext.getAgentCode());
		}
	}
}
