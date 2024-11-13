package com.ykb.payments.bill.transaction.institution.admin.web;

import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.transaction.adapter.constant.ResultConstant;
import com.ykb.payments.bill.transaction.adapter.core.utilities.DataResult;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionDebtTypeMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionDebtTypeService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionDebtTypeRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionDebtTypeRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.request.create.CreateInstitutionDebtTypeRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.request.update.UpdateInstitutionDebtTypeRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.response.InstitutionDebtTypeWebDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionDebtTypeDTO;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import javax.validation.Valid;
import java.util.List;

@RestController
@RequestMapping("/admin/institutionDebtTypes")
public class AdminInstitutionDebtTypeController {

    private final AdminInstitutionDebtTypeService institutionDebtTypeService;
    private final AdminInstitutionDebtTypeMapper institutionDebtTypeMapper;

    public AdminInstitutionDebtTypeController(AdminInstitutionDebtTypeService institutionDebtTypeService, AdminInstitutionDebtTypeMapper institutionDebtTypeMapper) {
        this.institutionDebtTypeService = institutionDebtTypeService;
        this.institutionDebtTypeMapper = institutionDebtTypeMapper;
    }


    @GetMapping("getAllInstitutionDebtTypes")
    public ResponseEntity<DataResult<List<InstitutionDebtTypeWebDTO>>> getAllInstitutionDebtTypes() {
        List<InstitutionDebtTypeDTO> institutionDebtTypeDTOS = institutionDebtTypeService.getAllInstitutionDebtTypes();
        List<InstitutionDebtTypeWebDTO> institutionDebtTypeWebDTOS= institutionDebtTypeMapper.toWebDTOList(institutionDebtTypeDTOS);
        DataResult<List<InstitutionDebtTypeWebDTO>> resultDTO=new DataResult<>
                (ResultConstant.DATA_LISTED.getMessage(),institutionDebtTypeWebDTOS);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }

    @GetMapping("getInstitutionDebtTypeById")
    public ResponseEntity<DataResult<InstitutionDebtTypeWebDTO>> getInstitutionDebtTypeById(@RequestParam Long id) throws MicroException{
        InstitutionDebtTypeDTO institutionDebtTypeDTO = institutionDebtTypeService.getInstitutionDebtTypeById(id);
        InstitutionDebtTypeWebDTO institutionDebtTypeWebDTO=institutionDebtTypeMapper.toWebDTO(institutionDebtTypeDTO);
        DataResult<InstitutionDebtTypeWebDTO> resultDTO= new DataResult<>(ResultConstant.DATA_RETRIEVED.getMessage(),institutionDebtTypeWebDTO );
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }


    @PostMapping("createInstitutionDebtType")
    public ResponseEntity<DataResult<InstitutionDebtTypeWebDTO>> createInstitutionDebtType(@RequestBody @Valid CreateInstitutionDebtTypeRequest request) throws MicroException {
        CreateInstitutionDebtTypeRequestDTO requestDTO =institutionDebtTypeMapper.toCreateInstitutionDebtTypeRequestDTO(request);
        InstitutionDebtTypeDTO institutionDebtTypeDTO = institutionDebtTypeService.createInstitutionDebtType(requestDTO);
        InstitutionDebtTypeWebDTO institutionDebtTypeWebDTO= institutionDebtTypeMapper.toWebDTO(institutionDebtTypeDTO);
        DataResult<InstitutionDebtTypeWebDTO> resultDTO= new DataResult<>(ResultConstant.INSTITUTION_DEBT_TYPE_CREATED.getMessage(),institutionDebtTypeWebDTO );
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }

    @PutMapping("updateInstitutionDebtType")
    public ResponseEntity<DataResult<InstitutionDebtTypeWebDTO>> updateInstitutionDebtType(@RequestBody @Valid UpdateInstitutionDebtTypeRequest request) throws MicroException {
        UpdateInstitutionDebtTypeRequestDTO requestDTO = institutionDebtTypeMapper.toUpdateInstitutionDebtTypeRequestDTO(request);
        InstitutionDebtTypeDTO institutionDebtTypeDTO = institutionDebtTypeService.updateInstitutionDebtType(requestDTO);
        InstitutionDebtTypeWebDTO institutionDebtTypeWebDTO= institutionDebtTypeMapper.toWebDTO(institutionDebtTypeDTO);
        DataResult<InstitutionDebtTypeWebDTO> resultDTO= new DataResult<>(ResultConstant.INSTITUTION_DEBT_TYPE_UPDATED.getMessage(),institutionDebtTypeWebDTO );
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }


}
