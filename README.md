package com.ykb.payments.bill.transaction.institution.admin.web;


import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.transaction.adapter.constant.ResultConstant;
import com.ykb.payments.bill.transaction.adapter.core.utilities.DataResult;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionPymMethodService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionPymMethodRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionPymMethodRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.request.create.CreateInstitutionPymMethodRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.request.update.UpdateInstitutionPymMethodRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.response.InstitutionPymMethodWebDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionPymMethodDTO;
import com.ykb.payments.bill.transaction.institution.mapper.InstitutionPymMethodMapper;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/admin/institutionPymMethod")
public class AdminInstitutionPymMethodController {

    private final AdminInstitutionPymMethodService adminInstitutionPymMethodService;
    private final InstitutionPymMethodMapper institutionPymMethodMapper;

    public AdminInstitutionPymMethodController(AdminInstitutionPymMethodService adminInstitutionPymMethodService, InstitutionPymMethodMapper institutionPymMethodMapper) {
        this.adminInstitutionPymMethodService = adminInstitutionPymMethodService;
        this.institutionPymMethodMapper = institutionPymMethodMapper;
    }

    @GetMapping("getAllInstitutionPymMethods")
    public ResponseEntity<DataResult<List<InstitutionPymMethodWebDTO>>> getAllInstitutionPymMethods() {
        List<InstitutionPymMethodDTO> dtoList= adminInstitutionPymMethodService.getAllInstitutionPymMethods();
        List<InstitutionPymMethodWebDTO> webDTOList= institutionPymMethodMapper.toWebDTOList(dtoList);
        DataResult<List<InstitutionPymMethodWebDTO>> resultDTO=
                new DataResult<>
                        (ResultConstant.DATA_LISTED.getMessage(),webDTOList);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }

    @GetMapping("getInstitutionPymMethodById")
    public ResponseEntity<DataResult<InstitutionPymMethodWebDTO>> getInstitutionPymMethodById(@RequestParam Long id){
        InstitutionPymMethodDTO dto= adminInstitutionPymMethodService.getInstitutionPymMethodById(id);
        InstitutionPymMethodWebDTO webDTO = institutionPymMethodMapper.toWebDTO(dto);
        DataResult<InstitutionPymMethodWebDTO> resultDTO= new DataResult<>(ResultConstant.DATA_RETRIEVED.getMessage(),webDTO);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }


    @PostMapping("createInstitutionPymMethod")
    public ResponseEntity<DataResult<InstitutionPymMethodWebDTO>> createInstitutionPymMethod(@RequestBody CreateInstitutionPymMethodRequest request) throws MicroException {
        CreateInstitutionPymMethodRequestDTO requestDTO= institutionPymMethodMapper.toRequestDTO(request);
        InstitutionPymMethodDTO dto= adminInstitutionPymMethodService.createInstitutionPymMethod(requestDTO);
        InstitutionPymMethodWebDTO webDTO= institutionPymMethodMapper.toWebDTO(dto);
        DataResult<InstitutionPymMethodWebDTO> resultDTO= new DataResult<>(ResultConstant.INSTITUTION_PYM_METHOD_CREATED.getMessage(),webDTO);
        return ResponseEntity.status(HttpStatus.CREATED).body(resultDTO);

    }

    @PutMapping("updateInstitutionPymMethod")
    public ResponseEntity<DataResult<InstitutionPymMethodWebDTO>> updateInstitutionPymMethod(@RequestBody UpdateInstitutionPymMethodRequest request) throws MicroException {

        UpdateInstitutionPymMethodRequestDTO requestDTO= institutionPymMethodMapper.toRequestDTO(request);
        InstitutionPymMethodDTO dto= adminInstitutionPymMethodService.updateInstitutionPymMethod(requestDTO);
        InstitutionPymMethodWebDTO webDTO= institutionPymMethodMapper.toWebDTO(dto);
        DataResult<InstitutionPymMethodWebDTO> resultDTO= new DataResult<>(ResultConstant.INSTITUTION_PYM_METHOD_UPDATED.getMessage(),webDTO);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);


    }

}
