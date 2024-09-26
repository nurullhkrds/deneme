@Entity
@Getter
@Setter
@Audited

public class InstitutionChannel extends UpdatableBaseEntity {

	@Id
	@Column(nullable = false, precision = 16, scale = 0)
	@SequenceGenerator(name = "INSTITUTION_CHANNEL_GENERATOR", sequenceName = "SEQ_INSTITUTION_CHANNEL", allocationSize = 1)
	@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "INSTITUTION_CHANNEL_GENERATOR")
	private Long id;

	@ManyToOne(fetch = FetchType.EAGER, optional = false)
	@JoinColumn(name = "INSTITUTION_DEBT_TYPE_ID", referencedColumnName = "ID")
	private InstitutionDebtType institutionDebtType;

	@ManyToOne(fetch = FetchType.EAGER, optional = false)
	@JoinColumn(name = "CHANNEL_CODE", referencedColumnName = "CODE")
	@Audited(targetAuditMode = RelationTargetAuditMode.NOT_AUDITED)
	private Channel channel;

	@Column(nullable = false)
	private Boolean isNewBillNeeded;

	@Column(nullable = false)
	private Boolean isPartialPaymentAllowed;

	@Column(nullable = false)
	private Boolean isOverPaymentAllowed;

	@Convert(converter = LocalTimeAttributeConverter.class)
	@Column(nullable = false)
	private LocalTime workingStartTime;
	
	@Convert(converter = LocalTimeAttributeConverter.class)
	@Column(nullable = false)
	private LocalTime workingFinishTime;

	@Column(nullable = false)
	private Boolean isActive;

}



@Entity
@Getter
@Setter
public class Channel {
	
	@Id
	@Column(nullable = false, length = 50)
	private String code;
	
	@Column(nullable = false, length = 100)
	private String name;
	
	@Column(nullable = false, length = 500)
	private String explanation;
	
	@Column(nullable = false)
	private Boolean isAdc;
	
	@Column(length = 30)
	private String accountingGroup;
	
/**	@OneToMany(mappedBy = "channel", fetch = FetchType.LAZY, cascade = CascadeType.ALL)
	private List<InstitutionChannel> institutionChannel;

	@OneToMany(mappedBy = "process", fetch = FetchType.LAZY, cascade = CascadeType.ALL)
	private List<ProcessChannel> processChannel;*/


}
@Entity
@Getter
@Setter
@Audited

public class InstitutionDebtType extends UpdatableBaseEntity {
	
	@Id
	@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "INSTITUTION_DEBT_TYPE_GENERATOR")
	@SequenceGenerator(name = "INSTITUTION_DEBT_TYPE_GENERATOR", sequenceName = "SEQ_INSTITUTION_DEBT_TYPE", allocationSize = 1)
	@Column(nullable = false, precision = 16, scale = 0)
	private Long id;
	
	@ManyToOne(optional = false, fetch = FetchType.EAGER)
	@JoinColumn(name = "INSTITUTION_ID", referencedColumnName = "ID")
	private Institution institution;
		
	@Column(nullable = false, length = 30)
	private String debtType;
	
	@Column(nullable = false, length = 500)
	private String explanation;
	
	@Column(nullable = false)
	private Boolean isActive;
	
/**	@OneToMany(mappedBy = "institutionDebtType", fetch = FetchType.LAZY, cascade = CascadeType.ALL)
	private List<InstitutionChannel> institutionChannelList;
	
	@OneToMany(mappedBy = "institutionDebtType", fetch = FetchType.EAGER, cascade = CascadeType.ALL)
	private InstitutionUserIntf institutionUserIntf;*/

}












@Getter
@Setter

public class CreateInstitutionChannelRequest extends BaseCreateWebRequest {
}
