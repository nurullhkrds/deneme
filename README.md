import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import javax.persistence.criteria.CriteriaBuilder;
import javax.persistence.criteria.CriteriaQuery;
import javax.persistence.criteria.Predicate;
import javax.persistence.criteria.Root;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.data.jpa.domain.Specification;

public class ReturnMapCriteriaTest {

    @Mock
    private Root<ReturnMap> root;
    
    @Mock
    private CriteriaQuery<?> query;
    
    @Mock
    private CriteriaBuilder cb;
    
    @Mock
    private Predicate predicate;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    public void testHasReturnMapCode_withValidCode() {
        String returnMapCode = "validCode";
        when(cb.equal(root.get("returnMapCode"), returnMapCode)).thenReturn(predicate);

        Specification<ReturnMap> spec = ReturnMapCriteria.hasReturnMapCode(returnMapCode);
        Predicate result = spec.toPredicate(root, query, cb);

        assertEquals(predicate, result);
    }

    @Test
    public void testHasReturnMapCode_withNullCode() {
        String returnMapCode = null;
        when(cb.conjunction()).thenReturn(predicate);

        Specification<ReturnMap> spec = ReturnMapCriteria.hasReturnMapCode(returnMapCode);
        Predicate result = spec.toPredicate(root, query, cb);

        assertEquals(predicate, result);
    }

    @Test
    public void testHasReturnMapCode_withEmptyCode() {
        String returnMapCode = "";
        when(cb.conjunction()).thenReturn(predicate);

        Specification<ReturnMap> spec = ReturnMapCriteria.hasReturnMapCode(returnMapCode);
        Predicate result = spec.toPredicate(root, query, cb);

        assertEquals(predicate, result);
    }

    @Test
    public void testHasBankErrorCode_withValidCode() {
        String bankReturnCode = "validCode";
        when(cb.equal(root.get("bankReturnCode"), bankReturnCode)).thenReturn(predicate);

        Specification<ReturnMap> spec = ReturnMapCriteria.hasBankErrorCode(bankReturnCode);
        Predicate result = spec.toPredicate(root, query, cb);

        assertEquals(predicate, result);
    }

    @Test
    public void testHasBankErrorCode_withNullCode() {
        String bankReturnCode = null;
        when(cb.conjunction()).thenReturn(predicate);

        Specification<ReturnMap> spec = ReturnMapCriteria.hasBankErrorCode(bankReturnCode);
        Predicate result = spec.toPredicate(root, query, cb);

        assertEquals(predicate, result);
    }

    @Test
    public void testHasBankErrorCode_withEmptyCode() {
        String bankReturnCode = "";
        when(cb.conjunction()).thenReturn(predicate);

        Specification<ReturnMap> spec = ReturnMapCriteria.hasBankErrorCode(bankReturnCode);
        Predicate result = spec.toPredicate(root, query, cb);

        assertEquals(predicate, result);
    }

    @Test
    public void testHasInstitutionErrorCode_withValidCode() {
        String institutionReturnCode = "validCode";
        when(cb.equal(root.get("institutionReturnCode"), institutionReturnCode)).thenReturn(predicate);

        Specification<ReturnMap> spec = ReturnMapCriteria.hasInstitutionErrorCode(institutionReturnCode);
        Predicate result = spec.toPredicate(root, query, cb);

        assertEquals(predicate, result);
    }

    @Test
    public void testHasInstitutionErrorCode_withNullCode() {
        String institutionReturnCode = null;
        when(cb.conjunction()).thenReturn(predicate);

        Specification<ReturnMap> spec = ReturnMapCriteria.hasInstitutionErrorCode(institutionReturnCode);
        Predicate result = spec.toPredicate(root, query, cb);

        assertEquals(predicate, result);
    }

    @Test
    public void testHasInstitutionErrorCode_withEmptyCode() {
        String institutionReturnCode = "";
        when(cb.conjunction()).thenReturn(predicate);

        Specification<ReturnMap> spec = ReturnMapCriteria.hasInstitutionErrorCode(institutionReturnCode);
        Predicate result = spec.toPredicate(root, query, cb);

        assertEquals(predicate, result);
    }
}
