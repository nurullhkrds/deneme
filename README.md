import static org.assertj.core.api.Assertions.assertThat;
import static org.mockito.Mockito.when;

import javax.persistence.criteria.CriteriaBuilder;
import javax.persistence.criteria.CriteriaQuery;
import javax.persistence.criteria.Predicate;
import javax.persistence.criteria.Root;
import javax.persistence.criteria.Path;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.data.jpa.domain.Specification;

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

    @Mock
    private Path<String> path;

    @BeforeEach
    void setUp() {
        // Setup mocks if needed
    }

    @Test
    void hasReturnMapCode_whenCodeIsNull_shouldReturnConjunction() {
        Specification<ReturnMap> spec = ReturnMapSpecifications.hasReturnMapCode(null);
        when(cb.conjunction()).thenReturn(predicate);
        Predicate result = spec.toPredicate(root, query, cb);
        assertThat(result).isEqualTo(predicate);
    }

    @Test
    void hasReturnMapCode_whenCodeIsEmpty_shouldReturnConjunction() {
        Specification<ReturnMap> spec = ReturnMapSpecifications.hasReturnMapCode("");
        when(cb.conjunction()).thenReturn(predicate);
        Predicate result = spec.toPredicate(root, query, cb);
        assertThat(result).isEqualTo(predicate);
    }

    @Test
    void hasReturnMapCode_whenCodeIsProvided_shouldReturnPredicate() {
        String code = "testCode";
        Specification<ReturnMap> spec = ReturnMapSpecifications.hasReturnMapCode(code);
        when(root.get("returnMapCode")).thenReturn(path);
        when(cb.lower(path)).thenReturn(path);
        when(cb.equal(path, code.toLowerCase())).thenReturn(predicate);

        Predicate result = spec.toPredicate(root, query, cb);
        assertThat(result).isEqualTo(predicate);
    }

    @Test
    void hasBankErrorCode_whenCodeIsNull_shouldReturnConjunction() {
        Specification<ReturnMap> spec = ReturnMapSpecifications.hasBankErrorCode(null);
        when(cb.conjunction()).thenReturn(predicate);
        Predicate result = spec.toPredicate(root, query, cb);
        assertThat(result).isEqualTo(predicate);
    }

    @Test
    void hasBankErrorCode_whenCodeIsEmpty_shouldReturnConjunction() {
        Specification<ReturnMap> spec = ReturnMapSpecifications.hasBankErrorCode("");
        when(cb.conjunction()).thenReturn(predicate);
        Predicate result = spec.toPredicate(root, query, cb);
        assertThat(result).isEqualTo(predicate);
    }

    @Test
    void hasBankErrorCode_whenCodeIsProvided_shouldReturnPredicate() {
        String code = "testBankCode";
        Specification<ReturnMap> spec = ReturnMapSpecifications.hasBankErrorCode(code);
        when(root.get("bankReturnCode")).thenReturn(path);
        when(cb.lower(path)).thenReturn(path);
        when(cb.equal(path, code.toLowerCase())).thenReturn(predicate);

        Predicate result = spec.toPredicate(root, query, cb);
        assertThat(result).isEqualTo(predicate);
    }

    @Test
    void hasInstitutionErrorCode_whenCodeIsNull_shouldReturnConjunction() {
        Specification<ReturnMap> spec = ReturnMapSpecifications.hasInstitutionErrorCode(null);
        when(cb.conjunction()).thenReturn(predicate);
        Predicate result = spec.toPredicate(root, query, cb);
        assertThat(result).isEqualTo(predicate);
    }

    @Test
    void hasInstitutionErrorCode_whenCodeIsEmpty_shouldReturnConjunction() {
        Specification<ReturnMap> spec = ReturnMapSpecifications.hasInstitutionErrorCode("");
        when(cb.conjunction()).thenReturn(predicate);
        Predicate result = spec.toPredicate(root, query, cb);
        assertThat(result).isEqualTo(predicate);
    }

    @Test
    void hasInstitutionErrorCode_whenCodeIsProvided_shouldReturnPredicate() {
        String code = "testInstitutionCode";
        Specification<ReturnMap> spec = ReturnMapSpecifications.hasInstitutionErrorCode(code);
        when(root.get("institutionReturnCode")).thenReturn(path);
        when(cb.lower(path)).thenReturn(path);
        when(cb.equal(path, code.toLowerCase())).thenReturn(predicate);

        Predicate result = spec.toPredicate(root, query, cb);
        assertThat(result).isEqualTo(predicate);
    }
}
