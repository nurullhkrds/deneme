package com.ykb.payments.bill.transaction.institution.admin.web;

import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.transaction.adapter.constant.ResultConstant;
import com.ykb.payments.bill.transaction.adapter.core.utilities.DataResult;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionUserIntfMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionUserIntfService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionUserIntfRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionUserIntfRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.request.create.CreateInstitutionUserIntfRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.request.update.UpdateInstitutionUserIntfRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.response.InstitutionUserIntfWebDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionUserIntfDTO;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/admin/InstitutionUserIntf")
public class AdminInstitutionUserIntfController {


    private final AdminInstitutionUserIntfService institutionUserIntfService;

    private final AdminInstitutionUserIntfMapper institutionUserIntfMapper;


    public AdminInstitutionUserIntfController(AdminInstitutionUserIntfService institutionUserIntfService, AdminInstitutionUserIntfMapper institutionUserIntfMapper) {
        this.institutionUserIntfService = institutionUserIntfService;
        this.institutionUserIntfMapper = institutionUserIntfMapper;
    }


    @GetMapping("getAllInstitutionUserIntfs")
    public ResponseEntity<DataResult<List<InstitutionUserIntfWebDTO>>> getAllInstitutionUserIntfs(){
        List<InstitutionUserIntfWebDTO> webDTO = institutionUserIntfService.getAllInstitutionUserIntfs();
        DataResult<List<InstitutionUserIntfWebDTO>> resultDTO= new DataResult<>(ResultConstant.DATA_LISTED.getMessage(),webDTO);
        return ResponseEntity.ok(resultDTO);
    }


    @GetMapping("getInstitutionUserIntfById")
    public ResponseEntity<DataResult<InstitutionUserIntfWebDTO>> getInstitutionUserIntfById(@RequestParam Long id){
        InstitutionUserIntfDTO dto= institutionUserIntfService.getInstitutionUserIntfById(id);
        InstitutionUserIntfWebDTO webDTO = institutionUserIntfMapper.toWebDTO(dto);
        DataResult<InstitutionUserIntfWebDTO> resultDTO= new DataResult<>(ResultConstant.DATA_LISTED.getMessage(),webDTO);
        return ResponseEntity.ok(resultDTO);
    }

    @PostMapping("createInstitutionUserIntf")
    public ResponseEntity<DataResult<InstitutionUserIntfWebDTO>> createInstitutionUserIntf(@RequestBody CreateInstitutionUserIntfRequest request) throws MicroException {
        CreateInstitutionUserIntfRequestDTO requestDTO = institutionUserIntfMapper.toCreateDTO(request);
        InstitutionUserIntfDTO dto= institutionUserIntfService.createInstitutionUserIntf(requestDTO);
        InstitutionUserIntfWebDTO webDTO = institutionUserIntfMapper.toWebDTO(dto);
        DataResult<InstitutionUserIntfWebDTO> resultDTO= new DataResult<>(ResultConstant.SUCCESSFULLY_ADDED.getMessage(),webDTO);
        return ResponseEntity.ok(resultDTO);
    }


    @PutMapping("updateInstitutionUserIntf")
    public ResponseEntity<DataResult<InstitutionUserIntfWebDTO>> updateInstitutionUserIntf(@RequestBody UpdateInstitutionUserIntfRequest request) throws MicroException{
        UpdateInstitutionUserIntfRequestDTO requestDTO = institutionUserIntfMapper.toUpdateDTO(request);
        InstitutionUserIntfDTO dto= institutionUserIntfService.updateInstitutionUserIntf(requestDTO);
        InstitutionUserIntfWebDTO webDTO = institutionUserIntfMapper.toWebDTO(dto);
        DataResult<InstitutionUserIntfWebDTO> resultDTO= new DataResult<>(ResultConstant.SUCCESSFULLY_UPDATED.getMessage(),webDTO);
        return ResponseEntity.ok(resultDTO);
    }


}
