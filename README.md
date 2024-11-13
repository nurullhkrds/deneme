package com.ykb.payments.bill.transaction.institution.admin.web;


import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.transaction.adapter.constant.ResultConstant;
import com.ykb.payments.bill.transaction.adapter.core.utilities.DataResult;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionProcessMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionProcessService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionProcessRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionProcessRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.request.create.CreateInstitutionProcessRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.request.update.UpdateInstitutionProcessRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.response.InstitutionProcessWebDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionProcessDTO;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/admin/InstitutionProcess")
public class AdminInstitutionProcessController {

    private final AdminInstitutionProcessService institutionProcessService;
    private final AdminInstitutionProcessMapper institutionProcessMapper;

    public AdminInstitutionProcessController(AdminInstitutionProcessService institutionProcessService, AdminInstitutionProcessMapper institutionProcessMapper) {
        this.institutionProcessService = institutionProcessService;
        this.institutionProcessMapper = institutionProcessMapper;
    }


    @GetMapping("/getAllInstitutionProcess")
    public ResponseEntity<DataResult<List<InstitutionProcessWebDTO>>> getAllInstitutionProcess(){
        List<InstitutionProcessDTO> dto= institutionProcessService.getAllInstitutionProcess();
        List<InstitutionProcessWebDTO> webDTO= institutionProcessMapper.toWebDTOList(dto);
        DataResult<List<InstitutionProcessWebDTO>> resultDTO =
                new DataResult<>(ResultConstant.DATA_LISTED.getMessage(),webDTO);
        return ResponseEntity.ok(resultDTO);
    }


    @GetMapping("/getInstitutionProcessById")
    public ResponseEntity<DataResult<InstitutionProcessWebDTO>> getInstitutionProcessById(@RequestParam Long id){
        InstitutionProcessDTO dto= institutionProcessService.getInstitutionProcessById(id);
        InstitutionProcessWebDTO webDTO= institutionProcessMapper.toWebDTO(dto);
        DataResult<InstitutionProcessWebDTO> resultDTO =
                new DataResult<>(ResultConstant.DATA_LISTED.getMessage(),webDTO);
        return ResponseEntity.ok(resultDTO);
    }


    @PostMapping("/createInstitutionProcess")
    public ResponseEntity<DataResult<InstitutionProcessWebDTO>> createInstitutionProcess(@RequestBody CreateInstitutionProcessRequest request)
    throws MicroException {
        CreateInstitutionProcessRequestDTO requestDTO= institutionProcessMapper.toRequestDTO(request);
        InstitutionProcessDTO dto= institutionProcessService.createInstitutionProcess(requestDTO);
        InstitutionProcessWebDTO webDTO= institutionProcessMapper.toWebDTO(dto);
        DataResult<InstitutionProcessWebDTO> resultDTO =
                new DataResult<>(ResultConstant.SUCCESSFULLY_ADDED.getMessage(),webDTO);
        return ResponseEntity.ok(resultDTO);
    }


    @PutMapping("/updateInstitutionProcess")
    public ResponseEntity<DataResult<InstitutionProcessWebDTO>> updateInstitutionProcess(@RequestBody UpdateInstitutionProcessRequest request)
            throws MicroException{
        UpdateInstitutionProcessRequestDTO requestDTO= institutionProcessMapper.toRequestDTO(request);
        InstitutionProcessDTO dto= institutionProcessService.updateInstitutionProcess(requestDTO);
        InstitutionProcessWebDTO webDTO= institutionProcessMapper.toWebDTO(dto);
        DataResult<InstitutionProcessWebDTO> resultDTO =
                new DataResult<>(ResultConstant.SUCCESSFULLY_UPDATED.getMessage(),webDTO);
        return ResponseEntity.ok(resultDTO);
    }



}
