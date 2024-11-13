package com.ykb.payments.bill.transaction.institution.admin.web;

import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.transaction.adapter.constant.ResultConstant;
import com.ykb.payments.bill.transaction.adapter.core.utilities.DataResult;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.request.create.CreateInstitutionRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.request.update.UpdateInstitutionRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.response.InstitutionWebDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionDTO;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import javax.validation.Valid;
import java.util.List;

@RestController
@RequestMapping("/admin/institutions")
public class AdminInstitutionController {
    private final AdminInstitutionService institutionService;
    private final AdminInstitutionMapper institutionMapper;

    public AdminInstitutionController(AdminInstitutionService institutionService, AdminInstitutionMapper institutionMapper) {
        this.institutionService = institutionService;
        this.institutionMapper = institutionMapper;
    }


    @GetMapping("/getAllInstitutions")
    public ResponseEntity<DataResult<List<InstitutionWebDTO>>> getAllInstitutions() {
        List<InstitutionDTO> institutionDTOS = institutionService.getAllInstitutions();
        List<InstitutionWebDTO> institutionWebDTOList = institutionMapper.toInstitutionWebDTOList(institutionDTOS);
        DataResult<List<InstitutionWebDTO>> resultDTO = new DataResult<>
                (ResultConstant.INSTITUTION_CREATED.getMessage(),institutionWebDTOList);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }

    @GetMapping("/getInstitutionById")
    public ResponseEntity<DataResult<InstitutionWebDTO>> getInstitutionById(@RequestParam Long id) throws MicroException {
        InstitutionDTO institutionDTO = institutionService.getInstitutionByIdTypeSecond(id);
        InstitutionWebDTO institutionWebDTO = institutionMapper.toInstitutionWebDTO(institutionDTO);
        DataResult<InstitutionWebDTO> resultDTO = new DataResult<>(ResultConstant.DATA_RETRIEVED.getMessage(), institutionWebDTO);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }


    @PostMapping("/createInstitution")
    public ResponseEntity<DataResult<InstitutionWebDTO>> createInstitution(@RequestBody @Valid CreateInstitutionRequest request) throws MicroException {
        CreateInstitutionRequestDTO requestDTO= institutionMapper.toCreateInstitutionRequestDTO(request);
        InstitutionDTO institutionDTO = institutionService.createInstitution(requestDTO);
        InstitutionWebDTO institutionWebDTO = institutionMapper.toInstitutionWebDTO(institutionDTO);
        DataResult<InstitutionWebDTO> resultDTO = new DataResult<>(ResultConstant.INSTITUTION_CREATED.getMessage(),institutionWebDTO);
        return ResponseEntity.status(HttpStatus.CREATED).body(resultDTO);
    }

    @PutMapping("/updateInstitution")
    public ResponseEntity<DataResult<InstitutionWebDTO>> updateInstitution(@RequestBody @Valid UpdateInstitutionRequest request) throws MicroException {
        UpdateInstitutionRequestDTO requestDTO= institutionMapper.toUpdateInstitutionRequestDTO(request);
        InstitutionDTO institutionDTO = institutionService.updateInstitution(requestDTO);
        InstitutionWebDTO institutionWebDTO = institutionMapper.toInstitutionWebDTO(institutionDTO);
        DataResult<InstitutionWebDTO> resultDTO=new DataResult<>(ResultConstant.INSTITUTION_UPDATED.getMessage(),institutionWebDTO);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);

    }


}
