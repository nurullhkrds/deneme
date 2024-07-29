@ExtendWith(MockitoExtension.class)
public class ReturnMapSpecificationTest {

    @Mock
    private Root<ReturnMap> root;

    @Mock
    private CriteriaQuery<?> query;

    @Mock
    private CriteriaBuilder cb;

    @Mock
    private Predicate predicate;

    @BeforeEach
    void setUp() {
        // Setup mocks if needed
    }

    @Test
    void hasReturnMapCode_whenCodeIsNull_shouldReturnConjunction() {
        Specification<ReturnMap> spec = ReturnMapSpecifications.hasReturnMapCode(null);
        Predicate result = spec.toPredicate(root, query, cb);
        assertThat(result).isEqualTo(cb.conjunction());
    }

    @Test
    void hasReturnMapCode_whenCodeIsEmpty_shouldReturnConjunction() {
        Specification<ReturnMap> spec = ReturnMapSpecifications.hasReturnMapCode("");
        Predicate result = spec.toPredicate(root, query, cb);
        assertThat(result).isEqualTo(cb.conjunction());
    }

    @Test
    void hasReturnMapCode_whenCodeIsProvided_shouldReturnPredicate() {
        String code = "testCode";
        Specification<ReturnMap> spec = ReturnMapSpecifications.hasReturnMapCode(code);
        when(cb.lower(root.get("returnMapCode"))).thenReturn(predicate);
        when(cb.equal(predicate, code.toLowerCase())).thenReturn(predicate);

        Predicate result = spec.toPredicate(root, query, cb);
        assertThat(result).isEqualTo(predicate);
    }

    @Test
    void hasBankErrorCode_whenCodeIsNull_shouldReturnConjunction() {
        Specification<ReturnMap> spec = ReturnMapSpecifications.hasBankErrorCode(null);
        Predicate result = spec.toPredicate(root, query, cb);
        assertThat(result).isEqualTo(cb.conjunction());
    }

    @Test
    void hasBankErrorCode_whenCodeIsEmpty_shouldReturnConjunction() {
        Specification<ReturnMap> spec = ReturnMapSpecifications.hasBankErrorCode("");
        Predicate result = spec.toPredicate(root, query, cb);
        assertThat(result).isEqualTo(cb.conjunction());
    }

    @Test
    void hasBankErrorCode_whenCodeIsProvided_shouldReturnPredicate() {
        String code = "testBankCode";
        Specification<ReturnMap> spec = ReturnMapSpecifications.hasBankErrorCode(code);
        when(cb.lower(root.get("bankReturnCode"))).thenReturn(predicate);
        when(cb.equal(predicate, code.toLowerCase())).thenReturn(predicate);

        Predicate result = spec.toPredicate(root, query, cb);
        assertThat(result).isEqualTo(predicate);
    }

    @Test
    void hasInstitutionErrorCode_whenCodeIsNull_shouldReturnConjunction() {
        Specification<ReturnMap> spec = ReturnMapSpecifications.hasInstitutionErrorCode(null);
        Predicate result = spec.toPredicate(root, query, cb);
        assertThat(result).isEqualTo(cb.conjunction());
    }

    @Test
    void hasInstitutionErrorCode_whenCodeIsEmpty_shouldReturnConjunction() {
        Specification<ReturnMap> spec = ReturnMapSpecifications.hasInstitutionErrorCode("");
        Predicate result = spec.toPredicate(root, query, cb);
        assertThat(result).isEqualTo(cb.conjunction());
    }

    @Test
    void hasInstitutionErrorCode_whenCodeIsProvided_shouldReturnPredicate() {
        String code = "testInstitutionCode";
        Specification<ReturnMap> spec = ReturnMapSpecifications.hasInstitutionErrorCode(code);
        when(cb.lower(root.get("institutionReturnCode"))).thenReturn(predicate);
        when(cb.equal(predicate, code.toLowerCase())).thenReturn(predicate);

        Predicate result = spec.toPredicate(root, query, cb);
        assertThat(result).isEqualTo(predicate);
    }
}
