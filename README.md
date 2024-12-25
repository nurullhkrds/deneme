SELECT * FROM REMOTE_SERVICE_LOG r 
WHERE r.institution_id = 50002 AND r.service_type = 'NOTIFY_PAYMENT' 
AND r.log_date BETWEEN to_date('26.09.2023', 'DD.MM.YYYY') AND to_date('26.09.2023', 'DD.MM.YYYY')

BU NORMAL SQL BANA VERİ GETİRİYOR... 



  @Override
    public List<RemoteServiceLog> getPaymentLogsByParameters(RemoteServiceLogRequest request) {
        DateTimeFormatter inputFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd");
        DateTimeFormatter outputFormatter = DateTimeFormatter.ofPattern("dd.MM.yyyy");

        LocalDate startDate = LocalDate.parse(request.getStartDate(), inputFormatter);
        LocalDate endDate = LocalDate.parse(request.getEndDate(), inputFormatter);

        String formattedStartDate = startDate.format(outputFormatter);
        String formattedEndDate = endDate.format(outputFormatter);

        return repository.findByInstitutionIdAndServiceTypeAndLogDateBetween(request.getInstitutionId(), request.getServiceType(), formattedStartDate, formattedEndDate);

    }


        @Query(value = "SELECT * FROM REMOTE_SERVICE_LOG r WHERE r.institution_id = :institutionId AND r.service_type = :serviceType AND r.log_date BETWEEN to_date(:startDate, 'DD.MM.YYYY') AND to_date(:endDate, 'DD.MM.YYYY')", nativeQuery = true)
    List<RemoteServiceLog> findByInstitutionIdAndServiceTypeAndLogDateBetween(Long institutionId, String serviceType, String startDate, String endDate);@Entity
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
