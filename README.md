package com.ykb.payments.bill.transaction.adapter.criteria;

import com.ykb.payments.bill.transaction.adapter.domain.ReturnMap;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.junit.jupiter.MockitoExtension;
import org.springframework.data.jpa.domain.Specification;

import javax.persistence.criteria.CriteriaBuilder;
import javax.persistence.criteria.CriteriaQuery;
import javax.persistence.criteria.Root;

import static org.mockito.Mockito.*;

@ExtendWith(MockitoExtension.class)
public class ReturnMapCriteriaTest {



    
    
    @Test
    void testHasBankErrorCode_withValidCode() {
        String bankReturnCode = "BANK123";
        Root<ReturnMap> root = mock(Root.class);
        CriteriaQuery<?> query = mock(CriteriaQuery.class);
        CriteriaBuilder cb = mock(CriteriaBuilder.class);

        Specification<ReturnMap> spec = ReturnMapCriteria.hasBankErrorCode(bankReturnCode);
        spec.toPredicate(root, query, cb);

        verify(cb).equal(cb.lower(root.get("bankReturnCode")), bankReturnCode.toLowerCase());
    }

    @Test
    void testHasBankErrorCode_withNullCode() {
        String bankReturnCode = null;
        Root<ReturnMap> root = mock(Root.class);
        CriteriaQuery<?> query = mock(CriteriaQuery.class);
        CriteriaBuilder cb = mock(CriteriaBuilder.class);

        Specification<ReturnMap> spec = ReturnMapCriteria.hasBankErrorCode(bankReturnCode);
        spec.toPredicate(root, query, cb);

        verify(cb).conjunction();
    }

    @Test
    void testHasInstitutionErrorCode_withValidCode() {
        String institutionReturnCode = "INST123";
        Root<ReturnMap> root = mock(Root.class);
        CriteriaQuery<?> query = mock(CriteriaQuery.class);
        CriteriaBuilder cb = mock(CriteriaBuilder.class);

        Specification<ReturnMap> spec = ReturnMapCriteria.hasInstitutionErrorCode(institutionReturnCode);
        spec.toPredicate(root, query, cb);

        verify(cb).equal(cb.lower(root.get("institutionReturnCode")), institutionReturnCode.toLowerCase());
    }

    @Test
    void testHasInstitutionErrorCode_withNullCode() {
        String institutionReturnCode = null;
        Root<ReturnMap> root = mock(Root.class);
        CriteriaQuery<?> query = mock(CriteriaQuery.class);
        CriteriaBuilder cb = mock(CriteriaBuilder.class);

        Specification<ReturnMap> spec = ReturnMapCriteria.hasInstitutionErrorCode(institutionReturnCode);
        spec.toPredicate(root, query, cb);

        verify(cb).conjunction();
    }
}
