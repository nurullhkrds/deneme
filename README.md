@Entity
@Getter
@Setter
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
public class ReturnMapCriteria {


    public static Specification<ReturnMap> hasReturnMapCode(String returnMapCode) {
        return (Root<ReturnMap> root, CriteriaQuery<?> query, CriteriaBuilder cb) -> {
            if (returnMapCode == null || returnMapCode.isEmpty()) {
                return cb.conjunction();
            }
            return cb.equal(cb.lower(root.get("returnMapCode")), returnMapCode.toLowerCase());
        };
    }

    public static Specification<ReturnMap> hasBankErrorCode(String bankReturnCode) {
        return (Root<ReturnMap> root, CriteriaQuery<?> query, CriteriaBuilder cb) -> {
            if (bankReturnCode == null || bankReturnCode.isEmpty()) {
                return cb.conjunction();
            }
            return cb.equal(cb.lower(root.get("bankReturnCode")), bankReturnCode.toLowerCase());
        };
    }

    public static Specification<ReturnMap> hasInstitutionErrorCode(String institutionReturnCode) {
        return (Root<ReturnMap> root, CriteriaQuery<?> query, CriteriaBuilder cb) -> {
            if (institutionReturnCode == null || institutionReturnCode.isEmpty()) {
                return cb.conjunction();
            }
            return cb.equal(cb.lower(root.get("institutionReturnCode")), institutionReturnCode.toLowerCase());
        };
    }

}


    @Override
    public DataResult<List<ReturnMapDTO>> searchReturnMaps(String returnMapCode, String bankReturnCode, String institutionReturnCode) {
        Specification<ReturnMap> spec = Specification.where(null);

        if (returnMapCode != null && !returnMapCode.isEmpty()) {
            spec = spec.and(ReturnMapCriteria.hasReturnMapCode(returnMapCode));
        }
        if (bankReturnCode != null && !bankReturnCode.isEmpty()) {
            spec = spec.and(ReturnMapCriteria.hasBankErrorCode(bankReturnCode));
        }
        if (institutionReturnCode != null && !institutionReturnCode.isEmpty()) {
            spec = spec.and(ReturnMapCriteria.hasInstitutionErrorCode(institutionReturnCode));
        }

        List<ReturnMap> returnMapList = returnMapRepository.findAll(spec);
        List<ReturnMapDTO> returnMapDTOList = returnMapMapper.toReturnMapDTOList(returnMapList);

        if (returnMapDTOList.isEmpty()) {
            return new ErrorDataResult<>("Listed is empty", returnMapDTOList, 200);
        }

        return new SuccessDataResult<>("Result listed", returnMapDTOList, 200);
    }
