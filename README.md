@Getter
@Setter
public class CreateInstitutionRequest extends BaseCreateWebRequest {

    @NotNull
    @Size(min = 1, max = 50)
    @Schema(description = "Institution code", example = "INST001", requiredMode = Schema.RequiredMode.REQUIRED)
    private String institutionCode;

    @NotNull
    @Schema(description = "Product code", example = "PRODUCT123", requiredMode = Schema.RequiredMode.REQUIRED)
    private String productCode;

    @NotNull
    @Schema(description = "Customer number", example = "123456789", requiredMode = Schema.RequiredMode.REQUIRED)
    private Long customerNo;

    @NotNull
    @Size(min = 1, max = 100)
    @Schema(description = "Name of the institution", example = "My Institution", requiredMode = Schema.RequiredMode.REQUIRED)
    private String name;

    @NotNull
    @Size(min = 1, max = 500)
    @Schema(description = "Explanation about the institution", example = "This is a description.", requiredMode = Schema.RequiredMode.REQUIRED)
    private String explanation;

    @NotNull
    @Schema(description = "Owner department code", example = "DEP001", requiredMode = Schema.RequiredMode.REQUIRED)
    private String ownerDepartmentCode;

    @Schema(description = "Protocol start date", example = "2024-01-01")
    private LocalDate protocolStartDate;

    @Schema(description = "Protocol end date", example = "2024-12-31")
    private LocalDate protocolEndDate;

    @NotNull
    @Schema(description = "Is reverse allowed", example = "true", requiredMode = Schema.RequiredMode.REQUIRED)
    private Boolean isReverseAllowed;

    @NotNull
    @Schema(description = "Is order allowed", example = "true", requiredMode = Schema.RequiredMode.REQUIRED)
    private Boolean isOrderAllowed;

    @NotNull
    @Schema(description = "Has debt type", example = "true", requiredMode = Schema.RequiredMode.REQUIRED)
    private Boolean hasDebtType;

    @Size(max = 1000)
    @Schema(description = "Icon text", example = "Institution Icon")
    private String iconText;

    @NotNull
    @Schema(description = "Is active", example = "true", requiredMode = Schema.RequiredMode.REQUIRED)
    private Boolean isActive;
}


import org.mapstruct.Mapper;
import org.mapstruct.Mapping;
import org.mapstruct.Mappings;

@Mapper(componentModel = "spring")
public interface AdapterInstitutionMapper {

    // Entity'den DTO'ya dönüşüm
    AdapterInstitutionDTO toInstitutionDTO(Institution entity);

    // DTO'dan Entity'ye dönüşüm
    Institution toInstitution(AdapterInstitutionDTO dto);

    // Request'ten Institution entity'sine dönüşüm
    @Mappings({
        @Mapping(source = "productCode", target = "product.code"),
        @Mapping(source = "ownerDepartmentCode", target = "ownerDepartment.code"),
        @Mapping(source = "createUser", target = "createdBy") // createUser request alanını createdBy entity alanına eşliyoruz
    })
    Institution toInstitution(CreateInstitutionRequest request);

    // Entity listelerini DTO listelerine dönüştürme
    List<AdapterInstitutionDTO> toInstitutionDTOList(List<Institution> entityList);
}




@Service
public class InstitutionService {

    @Autowired
    private InstitutionRepository institutionRepository;

    @Autowired
    private ProductRepository productRepository;

    @Autowired
    private OwnerDepartmentRepository ownerDepartmentRepository;

    @Autowired
    private AdapterInstitutionMapper institutionMapper;

    public DataResult<AdapterInstitutionDTO> createInstitution(CreateInstitutionRequest request) {
        // İlgili product ve ownerDepartment objelerini getiriyoruz
        Product product = productRepository.findByCode(request.getProductCode())
            .orElseThrow(() -> new ResourceNotFoundException("Product not found with code: " + request.getProductCode()));

        OwnerDepartment ownerDepartment = ownerDepartmentRepository.findByCode(request.getOwnerDepartmentCode())
            .orElseThrow(() -> new ResourceNotFoundException("Owner Department not found with code: " + request.getOwnerDepartmentCode()));

        // MapStruct ile request'ten entity'ye dönüştürme
        Institution institution = institutionMapper.toInstitution(request);

        // İlişkisel alanları manuel olarak set ediyoruz
        institution.setProduct(product);
        institution.setOwnerDepartment(ownerDepartment);
        institution.setCreateDate(LocalDateTime.now()); // Zaman bilgisini manuel set ediyoruz

        // Veritabanına kaydediyoruz
        institutionRepository.save(institution);

        // DTO'ya dönüştürüp sonucu döndürüyoruz
        AdapterInstitutionDTO dto = institutionMapper.toInstitutionDTO(institution);

        return new DataResult<>(dto, true, "Institution successfully created.");
    }
}
