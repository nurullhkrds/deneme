package com.ykb.payments.bill.transaction.institution.admin.web;

import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.transaction.adapter.constant.ResultConstant;
import com.ykb.payments.bill.transaction.adapter.core.utilities.DataResult;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionChnnlPymMthdAccMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionChnlPymMthdAccService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionChnlPymMthdAccRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionChnlPymMthdAccRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.request.create.CreateInstitutionChnlPymMthdAccRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.request.update.UpdateInstitutionChnlPymMthdAccRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.response.InstitutionChnlPymMthdAccWebDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionChnnlPymMthdAccDTO;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/admin/institutionChannelPymMethodsAcc")
public class AdminInstitutionChnlPymMthdAccController {

    private final AdminInstitutionChnlPymMthdAccService institutionChnlPymMthdAccService;
    private final AdminInstitutionChnnlPymMthdAccMapper institutionChnnlPymMthdAccMapper;

    public AdminInstitutionChnlPymMthdAccController(AdminInstitutionChnlPymMthdAccService institutionChnlPymMthdAccService
            , AdminInstitutionChnnlPymMthdAccMapper institutionChnnlPymMthdAccMapper) {
        this.institutionChnlPymMthdAccService = institutionChnlPymMthdAccService;
        this.institutionChnnlPymMthdAccMapper = institutionChnnlPymMthdAccMapper;
    }



    @GetMapping("getAllInstitutionChannelPymMethodsAcc")
    public ResponseEntity<DataResult<List<InstitutionChnlPymMthdAccWebDTO>>> getAllInstitutionChannelPymMethodsAcc() {
        List<InstitutionChnlPymMthdAccWebDTO> webDTOList = institutionChnlPymMthdAccService.getAllInstitutionChannelPymMethodsAcc();
        DataResult<List<InstitutionChnlPymMthdAccWebDTO>> resultDTO = new DataResult<>
                (ResultConstant.DATA_LISTED.getMessage(), webDTOList);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }

    @GetMapping("getInstitutionChannelPymMethodAccById")
    public ResponseEntity<DataResult<InstitutionChnlPymMthdAccWebDTO>> getInstitutionChannelPymMethodAccById(@RequestParam Long id) {
        InstitutionChnnlPymMthdAccDTO dto = institutionChnlPymMthdAccService.getInstitutionChannelPymMethodAccById(id);
        InstitutionChnlPymMthdAccWebDTO webDTO = institutionChnnlPymMthdAccMapper.toWebDTO(dto);
        DataResult<InstitutionChnlPymMthdAccWebDTO> resultDTO = new DataResult<>(ResultConstant.DATA_RETRIEVED.getMessage(), webDTO);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }


    @PostMapping("createInstitutionChannelPymMethodAcc")
    public ResponseEntity<DataResult<InstitutionChnlPymMthdAccWebDTO>> createInstitutionChannelPymMethodAcc (@RequestBody CreateInstitutionChnlPymMthdAccRequest request)
    throws MicroException {
        CreateInstitutionChnlPymMthdAccRequestDTO requestDTO = institutionChnnlPymMthdAccMapper.toRequestDTO(request);
        InstitutionChnnlPymMthdAccDTO dto = institutionChnlPymMthdAccService.createInstitutionChannelPymMethodAcc(requestDTO);
        InstitutionChnlPymMthdAccWebDTO webDTO=institutionChnnlPymMthdAccMapper.toWebDTO(dto);
        DataResult<InstitutionChnlPymMthdAccWebDTO> resultDTO = new DataResult<>(ResultConstant.INSTITUTION_CHNNL_PYM_MTHD_ACC_CREATED.getMessage(), webDTO);
        return ResponseEntity.status(HttpStatus.CREATED).body(resultDTO);
    }

    @PutMapping("updateInstitutionChannelPymMethodAcc")
    public ResponseEntity<DataResult<InstitutionChnlPymMthdAccWebDTO>> updateInstitutionChannelPymMethodAcc (@RequestBody UpdateInstitutionChnlPymMthdAccRequest request)
      throws MicroException{
        UpdateInstitutionChnlPymMthdAccRequestDTO requestDTO = institutionChnnlPymMthdAccMapper.toRequestDTO(request);
        InstitutionChnnlPymMthdAccDTO dto = institutionChnlPymMthdAccService.updateInstitutionChannelPymMethodAcc(requestDTO);
        InstitutionChnlPymMthdAccWebDTO webDTO=institutionChnnlPymMthdAccMapper.toWebDTO(dto);
        DataResult<InstitutionChnlPymMthdAccWebDTO> resultDTO = new DataResult<>(ResultConstant.INSTITUTION_CHNNL_PYM_MTHD_ACC_UPDATED.getMessage(), webDTO);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }




}
