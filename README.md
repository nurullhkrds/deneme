package com.ykb.payments.bill.transaction.adapter.criteria;

import com.ykb.payments.bill.transaction.adapter.domain.ReturnMap;
import com.ykb.payments.bill.transaction.adapter.enums.EnumReturnType;
import com.ykb.payments.bill.transaction.adapter.repository.ReturnMapRepository;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.data.jpa.domain.Specification;
import org.springframework.test.context.junit.jupiter.SpringExtension;

import java.util.List;

import static org.junit.jupiter.api.Assertions.assertEquals;

@ExtendWith(SpringExtension.class)
@DataJpaTest
public class ReturnMapCriteriaTest {

    @Autowired
    private ReturnMapRepository returnMapRepository;

    private ReturnMap returnMap1;
    private ReturnMap returnMap2;

    @BeforeEach
    void setUp() {
        returnMap1 = new ReturnMap();
        returnMap1.setReturnMapCode("code1");
        returnMap1.setBankReturnCode("bankCode1");
        returnMap1.setInstitutionReturnCode("instCode1");
        returnMap1.setReturnType(EnumReturnType.SUCCESS);
        returnMap1.setIsReversible(true);

        returnMap2 = new ReturnMap();
        returnMap2.setReturnMapCode("code2");
        returnMap2.setBankReturnCode("bankCode2");
        returnMap2.setInstitutionReturnCode("instCode2");
        returnMap2.setReturnType(EnumReturnType.ERROR);
        returnMap2.setIsReversible(false);

        returnMapRepository.save(returnMap1);
        returnMapRepository.save(returnMap2);
    }

    @Test
    void testHasReturnMapCode() {
        Specification<ReturnMap> spec = ReturnMapCriteria.hasReturnMapCode("code1");
        List<ReturnMap> results = returnMapRepository.findAll(spec);
        assertEquals(1, results.size());
        assertEquals("code1", results.get(0).getReturnMapCode());
    }

    @Test
    void testHasBankErrorCode() {
        Specification<ReturnMap> spec = ReturnMapCriteria.hasBankErrorCode("bankCode1");
        List<ReturnMap> results = returnMapRepository.findAll(spec);
        assertEquals(1, results.size());
        assertEquals("bankCode1", results.get(0).getBankReturnCode());
    }

    @Test
    void testHasInstitutionErrorCode() {
        Specification<ReturnMap> spec = ReturnMapCriteria.hasInstitutionErrorCode("instCode1");
        List<ReturnMap> results = returnMapRepository.findAll(spec);
        assertEquals(1, results.size());
        assertEquals("instCode1", results.get(0).getInstitutionReturnCode());
    }

    @Test
    void testEmptySpecifications() {
        Specification<ReturnMap> spec = Specification.where(ReturnMapCriteria.hasReturnMapCode(null))
                .and(ReturnMapCriteria.hasBankErrorCode(null))
                .and(ReturnMapCriteria.hasInstitutionErrorCode(null));

        List<ReturnMap> results = returnMapRepository.findAll(spec);
        assertEquals(2, results.size());  // As we didn't filter, all entries should be returned
    }
}
