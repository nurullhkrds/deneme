
@AllArgsConstructor
public class InstitutionFeatureWebDTO extends BaseWebDTO {
    private Long id;
    private InstitutionDTO institution;
    private FeatureDTO feature;
Make "feature" transient or serializable.
    private String featureValue;
    private Boolean isActive;
}
