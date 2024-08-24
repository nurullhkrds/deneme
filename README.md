import com.ykb.payments.bill.transaction.adapter.domain.ReturnMap;
import com.ykb.payments.bill.transaction.adapter.domain.ReturnMapDefinition;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.data.jpa.domain.Specification;

import javax.persistence.criteria.CriteriaBuilder;
import javax.persistence.criteria.CriteriaQuery;
import javax.persistence.criteria.Join;
import javax.persistence.criteria.JoinType;
import javax.persistence.criteria.Root;

import static org.mockito.Mockito.*;

@ExtendWith(MockitoExtension.class)
public class ReturnMapCriteriaTest {

    @Test
    void testHasReturnMapDefinitionCode_withValidCode() {
        String returnMapCode = "MAP123";
        Root<ReturnMap> root = mock(Root.class);
        CriteriaQuery<?> query = mock(CriteriaQuery.class);
        CriteriaBuilder cb = mock(CriteriaBuilder.class);
        Join<ReturnMap, ReturnMapDefinition> join = mock(Join.class);

        when(root.join("returnMapDefinition", JoinType.LEFT)).thenReturn(join);
        when(cb.lower(join.get("returnMapCode"))).thenReturn(mock(javax.persistence.criteria.Expression.class));
        when(cb.equal(any(javax.persistence.criteria.Expression.class), eq(returnMapCode.toLowerCase())))
                .thenReturn(mock(javax.persistence.criteria.Predicate.class));

        Specification<ReturnMap> spec = ReturnMapCriteria.hasReturnMapDefinitionCode(returnMapCode);
        spec.toPredicate(root, query, cb);

        verify(root).join("returnMapDefinition", JoinType.LEFT);
        verify(cb).equal(any(javax.persistence.criteria.Expression.class), eq(returnMapCode.toLowerCase()));
    }

    @Test
    void testHasReturnMapDefinitionCode_withNullCode() {
        String returnMapCode = null;
        Root<ReturnMap> root = mock(Root.class);
        CriteriaQuery<?> query = mock(CriteriaQuery.class);
        CriteriaBuilder cb = mock(CriteriaBuilder.class);

        Specification<ReturnMap> spec = ReturnMapCriteria.hasReturnMapDefinitionCode(returnMapCode);
        spec.toPredicate(root, query, cb);

        verify(cb).conjunction();
    }

    @Test
    void testHasReturnMapDefinitionCode_withEmptyCode() {
        String returnMapCode = "";
        Root<ReturnMap> root = mock(Root.class);
        CriteriaQuery<?> query = mock(CriteriaQuery.class);
        CriteriaBuilder cb = mock(CriteriaBuilder.class);

        Specification<ReturnMap> spec = ReturnMapCriteria.hasReturnMapDefinitionCode(returnMapCode);
        spec.toPredicate(root, query, cb);

        verify(cb).conjunction();
    }
}
