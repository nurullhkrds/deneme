package com.ykb.payments.bill.transaction.institution.admin.service.impl;

import com.ykb.architecture.micro.error.exception.DataConflictException;
import com.ykb.architecture.micro.error.exception.DataNotFoundException;
import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.common.exception.BillExceptionsUI;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionFeatureMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminFeatureService;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionFeatureService;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionFeatureRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionFeatureRequestDTO;
import com.ykb.payments.bill.transaction.institution.domain.InstitutionFeature;
import com.ykb.payments.bill.transaction.institution.dto.FeatureDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionFeatureDTO;
import com.ykb.payments.bill.transaction.institution.repository.InstitutionFeatureRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;

import java.time.LocalDateTime;
import java.util.List;
import java.util.Optional;

@Service
@RequiredArgsConstructor
public class AdminInstitutionFeatureServiceImpl implements AdminInstitutionFeatureService {

	private final InstitutionFeatureRepository institutionFeatureRepository;

	private final AdminInstitutionFeatureMapper institutionFeatureMapper;
	private final AdminInstitutionService institutionService;
	private final AdminFeatureService featureService;


	@Override
	public List<InstitutionFeatureDTO> getAllInstitutionFeatures() {
		List<InstitutionFeature> institutionFeatures = institutionFeatureRepository.findAll();
        return institutionFeatureMapper
				.toInstitutionFeatureDTOList(institutionFeatures);

	}

	@Override
	public InstitutionFeatureDTO getInstitutionFeatureById(Long institutionFeatureId) throws MicroException {
		Optional<InstitutionFeature> institutionFeature = institutionFeatureRepository.findById(institutionFeatureId);
        return institutionFeature.map(institutionFeatureMapper::toInstitutionFeatureDTO).orElse(null);
    }

	@Override
	public InstitutionFeatureDTO createInstitutionFeature(CreateInstitutionFeatureRequestDTO requestDTO) throws MicroException {

		Optional<InstitutionFeature> existingInstitutionFeature = institutionFeatureRepository
				.findByInstitutionIdAndFeatureCode(requestDTO.getInstitutionId(), requestDTO.getFeatureCode().getValue());
		if (existingInstitutionFeature.isPresent()) {
			throw new DataConflictException(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTION_FEATURE);
		}

		InstitutionDTO institutionDTO = institutionService.getInstitutionByIdTypeSecond(
				requestDTO.getInstitutionId());

		if (institutionDTO == null) {
			throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_NOT_FOUND);
		}

		FeatureDTO featureDTO = featureService.getFeatureByCode(requestDTO.getFeatureCode());
		InstitutionFeatureDTO institutionFeatureDTO = institutionFeatureMapper.toInstitutionFeatureDTO(requestDTO);
		institutionFeatureDTO.setInstitution(institutionDTO);
		institutionFeatureDTO.setFeature(featureDTO);
		institutionFeatureDTO.setCreateDate(LocalDateTime.now());

		InstitutionFeature institutionFeature = institutionFeatureMapper.toInstitutionFeature(institutionFeatureDTO);
		institutionFeature = institutionFeatureRepository.save(institutionFeature);
		return institutionFeatureMapper.toInstitutionFeatureDTO(institutionFeature);
	}

	@Override
	public InstitutionFeatureDTO updateInstitutionFeature(UpdateInstitutionFeatureRequestDTO requestDTO) throws MicroException {
		InstitutionFeatureDTO existingInstitutionFeatureDTO = getInstitutionFeatureById(requestDTO.getId());
		if (existingInstitutionFeatureDTO == null) {
			throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_FEATURE_NOT_FOUND);
		}

		Optional<InstitutionFeature> duplicateFeature = institutionFeatureRepository
				.findByInstitutionIdAndFeatureCode(requestDTO.getInstitutionId(), requestDTO.getFeatureCode().getValue());
		if (duplicateFeature.isPresent() && !duplicateFeature.get().getId().equals(requestDTO.getId())) {

			throw new DataConflictException(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTION_FEATURE);
		}

		InstitutionDTO institutionDTO = institutionService.getInstitutionByIdTypeSecond(requestDTO.getInstitutionId());

		if (institutionDTO == null) {
			throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_NOT_FOUND);
		}

		FeatureDTO featureDTO = featureService.getFeatureByCode(requestDTO.getFeatureCode());

		existingInstitutionFeatureDTO.setInstitution(institutionDTO);
		existingInstitutionFeatureDTO.setFeature(featureDTO);
		existingInstitutionFeatureDTO.setFeatureValue(requestDTO.getFeatureValue());
		existingInstitutionFeatureDTO.setIsActive(requestDTO.getIsActive());
		existingInstitutionFeatureDTO.setUpdatedBy(requestDTO.getUpdateUser());
		existingInstitutionFeatureDTO.setUpdateDate(LocalDateTime.now());

		InstitutionFeature updatedInstitutionFeature = institutionFeatureMapper.toInstitutionFeature(existingInstitutionFeatureDTO);
		updatedInstitutionFeature = institutionFeatureRepository.save(updatedInstitutionFeature);

		return institutionFeatureMapper.toInstitutionFeatureDTO(updatedInstitutionFeature);
	}


}
