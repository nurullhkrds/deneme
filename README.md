import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.data.jpa.domain.Specification;

import javax.persistence.criteria.CriteriaBuilder;
import javax.persistence.criteria.CriteriaQuery;
import javax.persistence.criteria.Root;

import static org.mockito.ArgumentMatchers.any;
import static org.mockito.ArgumentMatchers.eq;
import static org.mockito.Mockito.*;

@ExtendWith(MockitoExtension.class)
public class ReturnMapSpecificationTest {

    @Test
    void testHasReturnMapCode_withValidCode() {
        // Arrange
        String returnMapCode = "ABC123";
        Root<ReturnMap> root = mock(Root.class);
        CriteriaQuery<?> query = mock(CriteriaQuery.class);
        CriteriaBuilder cb = mock(CriteriaBuilder.class);
        
        // Act
        Specification<ReturnMap> spec = ReturnMapSpecifications.hasReturnMapCode(returnMapCode);
        spec.toPredicate(root, query, cb);
        
        // Assert
        verify(cb).equal(cb.lower(root.get("returnMapCode")), returnMapCode.toLowerCase());
    }

    @Test
    void testHasReturnMapCode_withNullCode() {
        // Arrange
        String returnMapCode = null;
        Root<ReturnMap> root = mock(Root.class);
        CriteriaQuery<?> query = mock(CriteriaQuery.class);
        CriteriaBuilder cb = mock(CriteriaBuilder.class);

        // Act
        Specification<ReturnMap> spec = ReturnMapSpecifications.hasReturnMapCode(returnMapCode);
        spec.toPredicate(root, query, cb);

        // Assert
        verify(cb).conjunction();
    }

    @Test
    void testHasBankErrorCode_withValidCode() {
        // Arrange
        String bankReturnCode = "BANK123";
        Root<ReturnMap> root = mock(Root.class);
        CriteriaQuery<?> query = mock(CriteriaQuery.class);
        CriteriaBuilder cb = mock(CriteriaBuilder.class);

        // Act
        Specification<ReturnMap> spec = ReturnMapSpecifications.hasBankErrorCode(bankReturnCode);
        spec.toPredicate(root, query, cb);

        // Assert
        verify(cb).equal(cb.lower(root.get("bankReturnCode")), bankReturnCode.toLowerCase());
    }

    @Test
    void testHasBankErrorCode_withNullCode() {
        // Arrange
        String bankReturnCode = null;
        Root<ReturnMap> root = mock(Root.class);
        CriteriaQuery<?> query = mock(CriteriaQuery.class);
        CriteriaBuilder cb = mock(CriteriaBuilder.class);

        // Act
        Specification<ReturnMap> spec = ReturnMapSpecifications.hasBankErrorCode(bankReturnCode);
        spec.toPredicate(root, query, cb);

        // Assert
        verify(cb).conjunction();
    }

    @Test
    void testHasInstitutionErrorCode_withValidCode() {
        // Arrange
        String institutionReturnCode = "INST123";
        Root<ReturnMap> root = mock(Root.class);
        CriteriaQuery<?> query = mock(CriteriaQuery.class);
        CriteriaBuilder cb = mock(CriteriaBuilder.class);

        // Act
        Specification<ReturnMap> spec = ReturnMapSpecifications.hasInstitutionErrorCode(institutionReturnCode);
        spec.toPredicate(root, query, cb);

        // Assert
        verify(cb).equal(cb.lower(root.get("institutionReturnCode")), institutionReturnCode.toLowerCase());
    }

    @Test
    void testHasInstitutionErrorCode_withNullCode() {
        // Arrange
        String institutionReturnCode = null;
        Root<ReturnMap> root = mock(Root.class);
        CriteriaQuery<?> query = mock(CriteriaQuery.class);
        CriteriaBuilder cb = mock(CriteriaBuilder.class);

        // Act
        Specification<ReturnMap> spec = ReturnMapSpecifications.hasInstitutionErrorCode(institutionReturnCode);
        spec.toPredicate(root, query, cb);

        // Assert
        verify(cb).conjunction();
    }
}
