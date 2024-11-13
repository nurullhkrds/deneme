package com.ykb.payments.bill.transaction.institution.admin.service.impl;

import com.ykb.architecture.micro.error.exception.DataNotFoundException;
import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.common.exception.BillExceptionsUI;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminFeatureMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminFeatureService;
import com.ykb.payments.bill.transaction.institution.domain.Feature;
import com.ykb.payments.bill.transaction.institution.dto.FeatureDTO;
import com.ykb.payments.bill.transaction.institution.enums.EnumFeatureCode;
import com.ykb.payments.bill.transaction.institution.repository.FeatureRepository;
import org.springframework.stereotype.Service;

@Service
public class AdminFeatureServiceImpl implements AdminFeatureService {


    private final FeatureRepository featureRepository;
    private final AdminFeatureMapper featureMapper;

    public AdminFeatureServiceImpl(FeatureRepository featureRepository, AdminFeatureMapper featureMapper) {
        this.featureRepository = featureRepository;
        this.featureMapper = featureMapper;
    }


    @Override
    public FeatureDTO getFeatureByCode(EnumFeatureCode code) throws MicroException {

        Feature entity= featureRepository.findByCode(code);
        if (entity == null){
            throw new  DataNotFoundException(BillExceptionsUI.ValidationExceptions.FEATURE_NOT_FOUND);
        }
        FeatureDTO dto = featureMapper.toFeatureDTO(entity);
        return dto;


    }
}
