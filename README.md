@Entity
@Getter
@Setter
@Audited
public class InstitutionCity extends UpdatableBaseEntity {
   
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE, generator = "INSTITUTION_CITY_GENERATOR")
    @SequenceGenerator(name = "INSTITUTION_CITY_GENERATOR", sequenceName = "SEQ_INSTITUTION_CITY", allocationSize = 1)
	@Column(nullable= false, name = "ID", precision = 16, scale = 0)
	private Long id;
        
    @ManyToOne (optional = false, fetch = FetchType.LAZY)
	@JoinColumn(name = "INSTITUTION_ID", referencedColumnName = "ID")
	private Institution institution;
	
    @ManyToOne (optional = false, fetch = FetchType.LAZY)
	@JoinColumn(name = "CITY_PLATE_CODE", referencedColumnName = "CODE")
	@Audited(targetAuditMode = RelationTargetAuditMode.NOT_AUDITED)
	private City city;
	
	@Column(nullable= false, length = 1)
	private Boolean isActive;

 
}
@Entity
@Getter
@Setter
@AllArgsConstructor
@NoArgsConstructor
public class City {
    
	@Id
	@Column(nullable= false, length = 50)
	private String code;
	
	@Column(nullable= false, length = 100)
	private String name;
	
/**	@OneToMany(mappedBy = "city", fetch = FetchType.LAZY, cascade = CascadeType.ALL)
	private List<InstitutionCity> institutionCityList;*/

}


@Getter
@Setter
public class CreateInstitutionCityRequest extends BaseCreateWebRequest {

    @NotNull
    @Schema(description = "ID of the institution", example = "1", required = true)
    private Long institutionId;
}
