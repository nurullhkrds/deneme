package com.ykb.payments.bill.transaction.institution.admin.service.impl;

import com.ykb.architecture.micro.error.exception.DataConflictException;
import com.ykb.architecture.micro.error.exception.DataNotFoundException;
import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.common.exception.BillExceptionsUI;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionService;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminOwnerDepartmentService;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminProductService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionRequestDTO;
import com.ykb.payments.bill.transaction.institution.domain.Institution;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionDTO;
import com.ykb.payments.bill.transaction.institution.dto.OwnerDepartmentDTO;
import com.ykb.payments.bill.transaction.institution.dto.ProductDTO;
import com.ykb.payments.bill.transaction.institution.repository.InstitutionRepository;
import lombok.RequiredArgsConstructor;
import org.springframework.stereotype.Service;

import java.time.LocalDateTime;
import java.util.List;
import java.util.Optional;

@Service
@RequiredArgsConstructor
public class AdminInstitutionServiceImpl implements AdminInstitutionService {


	private final InstitutionRepository institutionRepository;
	private final AdminInstitutionMapper institutionMapper;
	private final AdminProductService productService;
	private final AdminOwnerDepartmentService ownerDepartmentService;


	@Override
	public List<InstitutionDTO> getAllInstitutions() {
		List<Institution> institutionList = institutionRepository.findAll();
		return institutionMapper.toInstitutionDTOList(institutionList);
	}

	@Override
	public InstitutionDTO getInstitutionByIdTypeSecond(Long id) {
		Optional<Institution> institution = institutionRepository.findById(id);
        return institution.map(institutionMapper::toInstitutionDTO).orElse(null);
    }

	@Override
	public InstitutionDTO createInstitution(CreateInstitutionRequestDTO requestDTO) throws MicroException {
		Optional<Institution> existingInstitution = institutionRepository
				.findByProductCodeAndInstitutionCode(requestDTO.getProductCode()
						, requestDTO.getInstitutionCode());
		if (existingInstitution.isPresent()) {
			throw new DataConflictException(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTION_PRODUCT);
		}

		ProductDTO productDTO = productService.getProductByCode(requestDTO.getProductCode());
		OwnerDepartmentDTO ownerDepartmentDTO = ownerDepartmentService.getOwnerDepartmentByCode(requestDTO.getOwnerDepartmentCode());

		InstitutionDTO institutionDTO = institutionMapper.toInstitutionDTO(requestDTO);
		institutionDTO.setCreateDate(LocalDateTime.now());
		institutionDTO.setProduct(productDTO);
		institutionDTO.setOwnerDepartment(ownerDepartmentDTO);

		Institution institution = institutionMapper.toInstitution(institutionDTO);

		institutionRepository.save(institution);

		return  institutionMapper.toInstitutionDTO(institution);
	}

	@Override
	public InstitutionDTO updateInstitution(UpdateInstitutionRequestDTO requestDTO) throws MicroException {

		InstitutionDTO existingInstitutionDTO = getInstitutionByIdTypeSecond(requestDTO.getId());
		if (existingInstitutionDTO == null) {
			throw new DataNotFoundException(BillExceptionsUI.ValidationExceptions.INSTITUTION_NOT_FOUND);
		}

		Optional<Institution> duplicateInstitution = institutionRepository.findByProductCodeAndInstitutionCode(requestDTO.getProductCode(), requestDTO.getInstitutionCode());
		if (duplicateInstitution.isPresent() && !duplicateInstitution.get().getId().equals(requestDTO.getId())) {
			throw new DataConflictException(BillExceptionsUI.ValidationExceptions.DUPLICATE_INSTITUTION_PRODUCT);

		}

		ProductDTO productDTO = productService.getProductByCode(requestDTO.getProductCode());
		OwnerDepartmentDTO ownerDepartmentDTO = ownerDepartmentService.getOwnerDepartmentByCode(requestDTO.getOwnerDepartmentCode());

		existingInstitutionDTO.setInstitutionCode(requestDTO.getInstitutionCode());
		existingInstitutionDTO.setProduct(productDTO);
		existingInstitutionDTO.setCustomerNo(requestDTO.getCustomerNo());
		existingInstitutionDTO.setName(requestDTO.getName());
		existingInstitutionDTO.setExplanation(requestDTO.getExplanation());
		existingInstitutionDTO.setOwnerDepartment(ownerDepartmentDTO);
		existingInstitutionDTO.setProtocolStartDate(requestDTO.getProtocolStartDate());
		existingInstitutionDTO.setProtocolEndDate(requestDTO.getProtocolEndDate());
		existingInstitutionDTO.setIsReverseAllowed(requestDTO.getIsReverseAllowed());
		existingInstitutionDTO.setIsOrderAllowed(requestDTO.getIsOrderAllowed());
		existingInstitutionDTO.setHasDebtType(requestDTO.getHasDebtType());
		existingInstitutionDTO.setIconText(requestDTO.getIconText());
		existingInstitutionDTO.setIsActive(requestDTO.getIsActive());

		existingInstitutionDTO.setUpdateDate(LocalDateTime.now());
		existingInstitutionDTO.setUpdatedBy(requestDTO.getUpdateUser());


		Institution updatedInstitution = institutionMapper.toInstitution(existingInstitutionDTO);
		institutionRepository.save(updatedInstitution);

		return institutionMapper.toInstitutionDTO(updatedInstitution);
	}




}
