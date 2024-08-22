    Optional<List<ReturnMapDefinition>> findByAllReturnMapCode (String returnMapCode);
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
