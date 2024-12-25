@Entity
@Getter
@Setter
public class RemoteServiceLog extends UpdatableBaseEntity {

	@Id
	@Column(nullable = false, length = 16)
	@SequenceGenerator(name = "REMOTESERVICELOG_ID_GENERATOR", sequenceName = "SEQ_REMOTE_SERVICE_LOG", allocationSize = 100)
	@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "REMOTESERVICELOG_ID_GENERATOR")
	private Long id;

	@Column(length = 16)
	private Long institutionId;

	@Column(length = 50)
	private String serviceType;

	@Column(length = 50)
	private String subscriberNo;

	@Column(nullable = false)
	private LocalDate logDate;

	@Column(nullable = false)
	private Long duration;

	@Column(nullable = false)
	private String sendData;

	@Column(nullable = false)
	private String receivedData;

	@Column(length = 255)
	private String institutionReturnCode;

	@Column(length = 10)
	private String bankReturnCode;

	@Column(length = 100)
	private String additionalInfo;

	@Column(length = 16)
	private Long dataPowerTransactionId;

	@Column(length = 4)
	private String channelCode;

	@Column(length = 4)
	private String branchCode;

	@Column(length = 50)
	private String channelTransactionId;

	@Column(length = 100)
	private String channelSessionId;
}
