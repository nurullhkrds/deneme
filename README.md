import static org.junit.jupiter.api.Assertions.*;
import static org.mockito.Mockito.*;

import com.example.yourpackage.entity.ReturnMap;
import com.example.yourpackage.entity.ReturnMapDefinition;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.MockitoAnnotations;
import org.springframework.data.jpa.domain.Specification;

import javax.persistence.criteria.CriteriaBuilder;
import javax.persistence.criteria.CriteriaQuery;
import javax.persistence.criteria.Join;
import javax.persistence.criteria.Root;

public class ReturnMapSpecificationTest {

    private Root<ReturnMap> root;
    private CriteriaQuery<?> query;
    private CriteriaBuilder criteriaBuilder;
    private Join<ReturnMap, ReturnMapDefinition> join;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
        root = mock(Root.class);
        query = mock(CriteriaQuery.class);
        criteriaBuilder = mock(CriteriaBuilder.class);
        join = mock(Join.class);
    }

    @Test
    void testHasReturnMapDefinitionCode_WithCode() {
        String returnMapCode = "testCode";

        when(root.join("returnMapDefinition", JoinType.LEFT)).thenReturn(join);
        when(criteriaBuilder.lower(join.get("returnMapCode"))).thenReturn(mock(javax.persistence.criteria.Expression.class));
        when(criteriaBuilder.equal(any(javax.persistence.criteria.Expression.class), eq(returnMapCode.toLowerCase())))
                .thenReturn(mock(javax.persistence.criteria.Predicate.class));

        Specification<ReturnMap> specification = ReturnMapSpecification.hasReturnMapDefinitionCode(returnMapCode);
        specification.toPredicate(root, query, criteriaBuilder);

        verify(root).join("returnMapDefinition", JoinType.LEFT);
        verify(criteriaBuilder).equal(any(javax.persistence.criteria.Expression.class), eq(returnMapCode.toLowerCase()));
    }

    @Test
    void testHasReturnMapDefinitionCode_WithNullCode() {
        String returnMapCode = null;

        Specification<ReturnMap> specification = ReturnMapSpecification.hasReturnMapDefinitionCode(returnMapCode);
        specification.toPredicate(root, query, criteriaBuilder);

        verify(criteriaBuilder).conjunction();
    }

    @Test
    void testHasReturnMapDefinitionCode_WithEmptyCode() {
        String returnMapCode = "";

        Specification<ReturnMap> specification = ReturnMapSpecification.hasReturnMapDefinitionCode(returnMapCode);
        specification.toPredicate(root, query, criteriaBuilder);

        verify(criteriaBuilder).conjunction();
    }
}
