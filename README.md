package com.ykb.payments.bill.transaction.institution.admin.web;

import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.transaction.adapter.constant.ResultConstant;
import com.ykb.payments.bill.transaction.adapter.core.utilities.DataResult;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionFeatureMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionFeatureService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionFeatureRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionFeatureRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.request.create.CreateInstitutionFeatureRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.request.update.UpdateInstitutionFeatureRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.response.InstitutionFeatureWebDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionFeatureDTO;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import javax.validation.Valid;
import java.util.List;

@RestController
@RequestMapping("/admin/institutionFeatures")
public class AdminInstitutionFeatureController {

    private final AdminInstitutionFeatureService institutionFeatureService;
    private final AdminInstitutionFeatureMapper institutionFeatureMapper;


    public AdminInstitutionFeatureController(AdminInstitutionFeatureService institutionFeatureService, AdminInstitutionFeatureMapper institutionFeatureMapper) {
        this.institutionFeatureService = institutionFeatureService;
        this.institutionFeatureMapper = institutionFeatureMapper;
    }


    @GetMapping("/getAllInstitutionFeatures")
    public ResponseEntity<DataResult<List<InstitutionFeatureWebDTO>>> getAllInstitutionFeatures() {
        List<InstitutionFeatureDTO> institutionFeatureDTOS = institutionFeatureService.getAllInstitutionFeatures();
        List<InstitutionFeatureWebDTO> institutionFeatureWebDTOList = institutionFeatureMapper.toWebDTOList(institutionFeatureDTOS);
        DataResult<List<InstitutionFeatureWebDTO>> resultDTO = new DataResult<>
                (ResultConstant.DATA_LISTED.getMessage(), institutionFeatureWebDTOList);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }

    @GetMapping("/getInstitutionFeatureById")
    public ResponseEntity<DataResult<InstitutionFeatureWebDTO>> getInstitutionFeatureById(@RequestParam Long institutionFeatureId) throws MicroException {
        InstitutionFeatureDTO institutionFeatureDTO = institutionFeatureService.getInstitutionFeatureById(institutionFeatureId);
        InstitutionFeatureWebDTO institutionFeatureWebDTO = institutionFeatureMapper.toWebDTO(institutionFeatureDTO);
        DataResult<InstitutionFeatureWebDTO> resultDTO = new DataResult<>(ResultConstant.DATA_RETRIEVED.getMessage(), institutionFeatureWebDTO);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }

    @PostMapping("/createInstitutionFeature")
    public ResponseEntity<DataResult<InstitutionFeatureWebDTO>> createInstitutionFeature(@RequestBody @Valid CreateInstitutionFeatureRequest request) throws MicroException {
        CreateInstitutionFeatureRequestDTO requestDTO= institutionFeatureMapper.toCreateInstitutionFeatureRequestDTO(request);
        InstitutionFeatureDTO institutionFeatureDTO = institutionFeatureService.createInstitutionFeature(requestDTO);
        InstitutionFeatureWebDTO institutionFeatureWebDTO = institutionFeatureMapper.toWebDTO(institutionFeatureDTO);
        DataResult<InstitutionFeatureWebDTO> resultDTO = new DataResult<>(ResultConstant.INSTITUTION_FEATURE_CREATED.getMessage(),institutionFeatureWebDTO);
        return ResponseEntity.status(HttpStatus.CREATED).body(resultDTO);
    }

    @PutMapping("/updateInstitutionFeature")
    public ResponseEntity<DataResult<InstitutionFeatureWebDTO>> updateInstitutionFeature(@RequestBody @Valid UpdateInstitutionFeatureRequest request) throws MicroException {
        UpdateInstitutionFeatureRequestDTO requestDTO = institutionFeatureMapper.toUpdateInstitutionFeatureRequestDTO(request);
        InstitutionFeatureDTO institutionFeatureDTO = institutionFeatureService.updateInstitutionFeature(requestDTO);
        InstitutionFeatureWebDTO institutionFeatureWebDTO = institutionFeatureMapper.toWebDTO(institutionFeatureDTO);
        DataResult<InstitutionFeatureWebDTO> resultDTO = new DataResult<>(ResultConstant.INSTITUTION_FEATURE_UPDATED.getMessage(),institutionFeatureWebDTO);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }



}
