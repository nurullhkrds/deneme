package com.ykb.payments.bill.transaction.institution.admin.web;


import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.transaction.adapter.constant.ResultConstant;
import com.ykb.payments.bill.transaction.adapter.core.utilities.DataResult;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionChnlPymMethodMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionChnlPymMethodService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionChnlPymMethodRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionChnlPymMethodRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.request.create.CreateInstitutionChnlPymMethodRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.request.update.UpdateInstitutionChnlPymMethodRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.response.InstitutionChnlPymMethodWebDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionChannelPymMethodDTO;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/admin/institutionChannelPymMethods")
public class AdminInstitutionChnlPymMethodController {

    private final AdminInstitutionChnlPymMethodService institutionChnlPymMethodService;

    private final AdminInstitutionChnlPymMethodMapper institutionChnlPymMethodMapper;

    public AdminInstitutionChnlPymMethodController(AdminInstitutionChnlPymMethodService institutionChnlPymMethodService, AdminInstitutionChnlPymMethodMapper institutionChnlPymMethodMapper) {
        this.institutionChnlPymMethodService = institutionChnlPymMethodService;
        this.institutionChnlPymMethodMapper = institutionChnlPymMethodMapper;
    }

    @GetMapping("getAllInstitutionChannelPymMethods")
    public ResponseEntity<DataResult<List<InstitutionChnlPymMethodWebDTO>>> getAllInstitutionChannelPymMethods() {
        List<InstitutionChannelPymMethodDTO> dtoList = institutionChnlPymMethodService.getAll();
        List<InstitutionChnlPymMethodWebDTO> webDTOList = institutionChnlPymMethodMapper.toWebDTOList(dtoList);
        DataResult<List<InstitutionChnlPymMethodWebDTO>> resultDTO = new DataResult<>
                (ResultConstant.DATA_LISTED.getMessage(), webDTOList);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }


    @GetMapping("getInstitutionChannelPymMethodById")
    public ResponseEntity<DataResult<InstitutionChnlPymMethodWebDTO>> getInstitutionChannelPymMethodById(@RequestParam Long id){
        InstitutionChannelPymMethodDTO dto= institutionChnlPymMethodService.getInstitutionChannelPymMethodById(id);
        InstitutionChnlPymMethodWebDTO webDTO = institutionChnlPymMethodMapper.toWebDTO(dto);
        DataResult<InstitutionChnlPymMethodWebDTO> resultDTO= new DataResult<>(ResultConstant.DATA_RETRIEVED.getMessage(),webDTO);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }

    @PostMapping("createInstitutionChannelPymMethod")
    public ResponseEntity<DataResult<InstitutionChnlPymMethodWebDTO>> createInstitutionChannelPymMethod(@RequestBody CreateInstitutionChnlPymMethodRequest request) throws MicroException {
        CreateInstitutionChnlPymMethodRequestDTO requestDTO= institutionChnlPymMethodMapper.toRequestDTO(request);
        InstitutionChannelPymMethodDTO dto= institutionChnlPymMethodService.createInstitutionChannelPymMethod(requestDTO);
        InstitutionChnlPymMethodWebDTO webDTO= institutionChnlPymMethodMapper.toWebDTO(dto);
        DataResult<InstitutionChnlPymMethodWebDTO> resultDTO= new DataResult<>
                (ResultConstant.INSTITUTION_CHANNEL_PYM_METHOD_CREATED.getMessage(),webDTO);
        return ResponseEntity.status(HttpStatus.CREATED).body(resultDTO);

    }


    @PutMapping("updateInstitutionChannelPymMethod")
    ResponseEntity<DataResult<InstitutionChnlPymMethodWebDTO>> updateInstitutionChannelPymMethod(@RequestBody UpdateInstitutionChnlPymMethodRequest request) throws MicroException {
        UpdateInstitutionChnlPymMethodRequestDTO requestDTO = institutionChnlPymMethodMapper.toRequestDTO(request);
        InstitutionChannelPymMethodDTO dto= institutionChnlPymMethodService.updateInstitutionChannelPymMethod(requestDTO);
        InstitutionChnlPymMethodWebDTO webDTO= institutionChnlPymMethodMapper.toWebDTO(dto);
        DataResult<InstitutionChnlPymMethodWebDTO> resultDTO= new DataResult<>
                (ResultConstant.INSTITUTION_CHANNEL_PYM_METHOD_UPDATED.getMessage(),webDTO);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);


    }






}
