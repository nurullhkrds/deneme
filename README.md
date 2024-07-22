@Component
public class BaseTransactionalEntityListener {

	@Autowired
	private RequestContext requestContext;

	@PrePersist
	public void prePersist(BaseTransactionalEntity baseTransactionEntity) {

		if (StringUtils.isEmpty(baseTransactionEntity.getBranchCode())) {
			baseTransactionEntity.setBranchCode(requestContext.getOperatingBranchCode());
		}

		if (StringUtils.isEmpty(baseTransactionEntity.getChannelCode())) {
			baseTransactionEntity.setChannelCode(requestContext.getChannelCode());
		}

		if (StringUtils.isEmpty(baseTransactionEntity.getChannelSessionId())) {
			baseTransactionEntity.setChannelSessionId(requestContext.getChannelSessionId());
		}

		if (StringUtils.isEmpty(baseTransactionEntity.getChannelTransactionId())) {
			baseTransactionEntity.setChannelTransactionId(requestContext.getChannelTransactionId());
		}
	}
}
