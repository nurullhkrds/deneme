@Entity
@Getter
@Setter
@Audited
@DynamicUpdate
public class ReturnMap extends UpdatableBaseEntity {

	@Id
	@Column(nullable = false, length = 16)
	@SequenceGenerator(name = "RETURN_MAP_ID_GENERATOR", sequenceName = "SEQ_RETURN_MAP", allocationSize = 1)
	@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "RETURN_MAP_ID_GENERATOR")
	private Long id;

	@Column(length = 50, nullable = false)
	private String returnMapCode;

	@Column(length = 255)
	private String institutionReturnCode;

	@Column(length = 500)
	private String institutionReturnText;

	@Column(length = 10)
	private String bankReturnCode;

	@Column(length = 250)
	private String bankReturnText;

	@Column(name = "RETURN_TYPE")
	@Convert(converter = EnumReturnTypeConverter.class)
	private EnumReturnType returnType;

	@Column(nullable = false)
	private Boolean isReversible;


	@ManyToOne(fetch = FetchType.LAZY)
	@JoinColumn(name = "return_map_definition_id",referencedColumnName = "ID", nullable = false)
	private ReturnMapDefinition returnMapDefinition;



}


@Entity
@Getter
@Setter
@Audited
@DynamicUpdate
public class ReturnMapDefinition extends UpdatableBaseEntity {

    @Id
    @Column(nullable = false, length = 16)
    @SequenceGenerator(name = "RETURN_MAP_DEFINITION_ID_GENERATOR", sequenceName = "SEQ_RETURN_MAP_DEFINITION", allocationSize = 1)
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "RETURN_MAP_DEFINITION_ID_GENERATOR")
    private Long id;

    @Column(length = 50, nullable = false)
    private String returnMapCode;

    @Column(nullable = false)
    private Boolean isActive;


}
