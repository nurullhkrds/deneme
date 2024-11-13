package com.ykb.payments.bill.transaction.institution.admin.service.impl;

import com.ykb.architecture.micro.error.exception.DataConflictException;
import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.common.exception.BillExceptionsUI;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionCityMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminCityService;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionCityService;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionCityRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionCityRequestDTO;
import com.ykb.payments.bill.transaction.institution.domain.InstitutionCity;
import com.ykb.payments.bill.transaction.institution.dto.CityDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionCityDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionDTO;
import com.ykb.payments.bill.transaction.institution.repository.InstitutionCityRepository;
import org.springframework.stereotype.Service;

import java.time.LocalDateTime;
import java.util.List;
import java.util.Optional;

@Service
public class AdminInstitutionCityServiceImpl implements AdminInstitutionCityService {

    private final InstitutionCityRepository institutionCityRepository;
    private final AdminInstitutionCityMapper institutionCityMapper;
    private final AdminInstitutionService institutionService;
    private final AdminCityService cityService;

    public AdminInstitutionCityServiceImpl(InstitutionCityRepository institutionCityRepository, AdminInstitutionCityMapper institutionCityMapper, AdminInstitutionService institutionService, AdminCityServiceImpl cityService) {
        this.institutionCityRepository = institutionCityRepository;
        this.institutionCityMapper = institutionCityMapper;
        this.institutionService = institutionService;
        this.cityService = cityService;
    }

    @Override
    public List<InstitutionCityDTO> getAllInstitutionCities() {

        return institutionCityMapper.toDTOList(institutionCityRepository.findAll());
    }

    @Override
    public InstitutionCityDTO getInstitutionCityById(Long institutionCityId) {
        return institutionCityMapper.toInstitutionCityDTO(institutionCityRepository.findById(institutionCityId).orElse(null));
    }

    @Override
    public InstitutionCityDTO createInstitutionCity(CreateInstitutionCityRequestDTO requestDTO)  throws MicroException {
        Optional<InstitutionCity> existingInstitutionCity = institutionCityRepository
                .findByInstitutionIdAndCityCode(requestDTO.getInstitutionId(), requestDTO.getCode());
        if (existingInstitutionCity.isPresent()) {
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTION_CITY);

        }

        InstitutionDTO institutionDTO = institutionService.getInstitutionByIdTypeSecond(requestDTO.getInstitutionId());
        if (institutionDTO == null) {
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.INSTITUTION_NOT_FOUND);
        }

        CityDTO cityDTO = cityService.getCityByCode(requestDTO.getCode());

        InstitutionCityDTO institutionCityDTO = institutionCityMapper.toDTO(requestDTO);
        institutionCityDTO.setInstitution(institutionDTO);
        institutionCityDTO.setCity(cityDTO);
        institutionCityDTO.setCreateDate(LocalDateTime.now());

        InstitutionCity savedInstitutionCity = institutionCityMapper.toInstitutionCity(institutionCityDTO);
        savedInstitutionCity = institutionCityRepository.save(savedInstitutionCity);
        return institutionCityMapper.toInstitutionCityDTO(savedInstitutionCity);
    }

    @Override
    public InstitutionCityDTO updateInstitutionCity(UpdateInstitutionCityRequestDTO requestDTO) throws MicroException {
        InstitutionCityDTO existingInstitutionCityDTO = getInstitutionCityById(requestDTO.getId());
        if (existingInstitutionCityDTO == null) {
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.INSTITUTION_CITY_NOT_FOUND);
        }

        Optional<InstitutionCity> duplicateInstitutionCity = institutionCityRepository
                .findByInstitutionIdAndCityCode(requestDTO.getInstitutionId(), requestDTO.getCode());
        if (duplicateInstitutionCity.isPresent() && !duplicateInstitutionCity.get().getId().equals(requestDTO.getId())) {
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTION_CITY);
        }

        InstitutionDTO institutionDTO = institutionService.getInstitutionByIdTypeSecond(requestDTO.getInstitutionId());

        if (institutionDTO == null) {
            throw new DataConflictException(BillExceptionsUI.ValidationExceptions.INSTITUTION_NOT_FOUND);
        }
        CityDTO cityDTO = cityService.getCityByCode(requestDTO.getCode());

        existingInstitutionCityDTO.setInstitution(institutionDTO);
        existingInstitutionCityDTO.setCity(cityDTO);
        existingInstitutionCityDTO.setUpdateDate(LocalDateTime.now());
        existingInstitutionCityDTO.setUpdatedBy(requestDTO.getUpdateUser());
        existingInstitutionCityDTO.setIsActive(requestDTO.getIsActive());

        InstitutionCity updatedInstitutionCity = institutionCityMapper.toInstitutionCity(existingInstitutionCityDTO);

        updatedInstitutionCity= institutionCityRepository.save(updatedInstitutionCity);
        return institutionCityMapper.toInstitutionCityDTO(updatedInstitutionCity);

    }
}
