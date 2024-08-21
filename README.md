@Id
	@Column(nullable = false, length = 16)
	@SequenceGenerator(name = "RETURN_MAP_ID_GENERATOR", sequenceName = "SEQ_RETURN_MAP", allocationSize = 1)
	@GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "RETURN_MAP_ID_GENERATOR")
	private Long id;
