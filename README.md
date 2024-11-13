package com.ykb.payments.bill.transaction.institution.admin.service.impl;

import com.ykb.architecture.micro.error.exception.DataConflictException;
import com.ykb.architecture.micro.error.exception.DataNotFoundException;
import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.common.exception.BillExceptionsUI;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionDebtTypeMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionDebtTypeService;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionDebtTypeRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionDebtTypeRequestDTO;
import com.ykb.payments.bill.transaction.institution.domain.InstitutionDebtType;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionDebtTypeDTO;
import com.ykb.payments.bill.transaction.institution.repository.InstitutionDebtTypeRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;

import java.time.LocalDateTime;
import java.util.List;
import java.util.Optional;

@Service
@RequiredArgsConstructor
public class AdminInstitutionDebtTypeServiceImpl implements AdminInstitutionDebtTypeService {

	private final InstitutionDebtTypeRepository institutionDebtTypeRepository;
	private final AdminInstitutionDebtTypeMapper institutionDebtTypeMapper;
	private final AdminInstitutionService institutionService;


	@Override
	public List<InstitutionDebtTypeDTO> getAllInstitutionDebtTypes() {
		List<InstitutionDebtType> institutionDebtTypeList=institutionDebtTypeRepository.findAll();
		return institutionDebtTypeMapper.toDTOList(institutionDebtTypeList);
	}

	@Override
	public InstitutionDebtTypeDTO getInstitutionDebtTypeById(Long id) throws MicroException {
		Optional <InstitutionDebtType> optionalInstitutionDebtType=institutionDebtTypeRepository.findById(id);

        return optionalInstitutionDebtType.map(institutionDebtTypeMapper::toDTO).orElse(null);

    }

	@Override
	public InstitutionDebtTypeDTO createInstitutionDebtType(CreateInstitutionDebtTypeRequestDTO requestDTO) throws MicroException {
		Optional<InstitutionDebtType> existingInstitutionDebtType = institutionDebtTypeRepository
				.findByInstitutionIdAndDebtTypeCode(requestDTO.getInstitutionId(), requestDTO.getDebtType());
		if (existingInstitutionDebtType.isPresent()) {
			throw new DataConflictException(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTION_DEBT_TYPE);
		}

		InstitutionDTO institutionDTO = institutionService.getInstitutionByIdTypeSecond(requestDTO.getInstitutionId());

		if(institutionDTO == null) {
			throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_NOT_FOUND);
		}

		InstitutionDebtTypeDTO dto = institutionDebtTypeMapper.toDTO(requestDTO);
		dto.setInstitution(institutionDTO);
		dto.setCreateDate(LocalDateTime.now());

		InstitutionDebtType entity = institutionDebtTypeMapper.toEntity(dto);
		entity = institutionDebtTypeRepository.save(entity);

		return institutionDebtTypeMapper.toDTO(entity);

	}

	@Override
	public InstitutionDebtTypeDTO updateInstitutionDebtType(UpdateInstitutionDebtTypeRequestDTO requestDTO) throws MicroException {
		InstitutionDebtTypeDTO existingDebtTypeDTO = getInstitutionDebtTypeById(requestDTO.getId());

		if (existingDebtTypeDTO == null) {
			throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_DEBT_TYPE_NOT_FOUND);
		}


		Optional<InstitutionDebtType> duplicateDebtType = institutionDebtTypeRepository
				.findByInstitutionIdAndDebtTypeCode(requestDTO.getInstitutionId(), requestDTO.getDebtType());
		if (duplicateDebtType.isPresent() && !duplicateDebtType.get().getId().equals(requestDTO.getId())) {
			throw new DataConflictException(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTION_DEBT_TYPE);

		}

		InstitutionDTO institutionDTO = institutionService.getInstitutionByIdTypeSecond(requestDTO.getInstitutionId());
		if (institutionDTO == null) {
			throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_NOT_FOUND);

		}

		existingDebtTypeDTO.setInstitution(institutionDTO);
		existingDebtTypeDTO.setUpdatedBy(requestDTO.getUpdateUser());
		existingDebtTypeDTO.setUpdateDate(LocalDateTime.now());
		existingDebtTypeDTO.setExplanation(requestDTO.getExplanation());
		existingDebtTypeDTO.setIsActive(requestDTO.getIsActive());

		InstitutionDebtType updatedDebtType = institutionDebtTypeMapper.toEntity(existingDebtTypeDTO);
		updatedDebtType = institutionDebtTypeRepository.save(updatedDebtType);

		return institutionDebtTypeMapper.toDTO(updatedDebtType);
	}



}
