package com.ykb.payments.bill.transaction.institution.admin.web;

import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.transaction.adapter.constant.ResultConstant;
import com.ykb.payments.bill.transaction.adapter.core.utilities.DataResult;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionChnnlPymMthdPscMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionChnlPymMthdAPscService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionChnlPymMthdPscRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionChnlPymMthdPscRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.request.create.CreateInstitutionChnlPymMthdPscRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.request.update.UpdateInstitutionChnlPymMthdPscRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.response.InstitutionChnlPymMthdPscWebDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionChnnlPymMthdPscDTO;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/admin/institutionChannelPymMethodsPsc")
public class AdminInstitutionChnlPymMthdPscController {

    private final AdminInstitutionChnlPymMthdAPscService institutionChnlPymMthdPscService;
    private final AdminInstitutionChnnlPymMthdPscMapper institutionChnnlPymMthdPscMapper;

    public AdminInstitutionChnlPymMthdPscController(AdminInstitutionChnlPymMthdAPscService institutionChnlPymMthdPscService, AdminInstitutionChnnlPymMthdPscMapper institutionChnnlPymMthdPscMapper) {
        this.institutionChnlPymMthdPscService = institutionChnlPymMthdPscService;
        this.institutionChnnlPymMthdPscMapper = institutionChnnlPymMthdPscMapper;
    }


    @GetMapping("getAllInstitutionChannelPymMethodsPsc")
    public ResponseEntity<DataResult<List<InstitutionChnlPymMthdPscWebDTO>>> getAllInstitutionChannelPymMethodsPsc() {
        List<InstitutionChnlPymMthdPscWebDTO> webDTOList = institutionChnlPymMthdPscService.getAllInstitutionChnlPymMthdAPsc();
        DataResult<List<InstitutionChnlPymMthdPscWebDTO>> resultDTO = new DataResult<>
                (ResultConstant.DATA_LISTED.getMessage(), webDTOList);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }

    @GetMapping("getInstitutionChannelPymMethodPscById")
    public ResponseEntity<DataResult<InstitutionChnlPymMthdPscWebDTO>> getInstitutionChannelPymMethodPscById(Long id) {
        InstitutionChnnlPymMthdPscDTO dto = institutionChnlPymMthdPscService.getInstitutionChnlPymMthdAPscById(id);
        InstitutionChnlPymMthdPscWebDTO webDTO = institutionChnnlPymMthdPscMapper.toWebDTO(dto);
        DataResult<InstitutionChnlPymMthdPscWebDTO> resultDTO = new DataResult<>
                (ResultConstant.DATA_RETRIEVED.getMessage(), webDTO);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }

    @PostMapping("createInstitutionChannelPymMethodPsc")
    public ResponseEntity<DataResult<InstitutionChnlPymMthdPscWebDTO>> createInstitutionChannelPymMethodPsc(@RequestBody CreateInstitutionChnlPymMthdPscRequest request) throws MicroException {

        CreateInstitutionChnlPymMthdPscRequestDTO requestDTO=institutionChnnlPymMthdPscMapper.toRequestDTO(request);
        InstitutionChnnlPymMthdPscDTO dto = institutionChnlPymMthdPscService.createInstitutionChnlPymMthdPsc(requestDTO);
        InstitutionChnlPymMthdPscWebDTO webDTO =institutionChnnlPymMthdPscMapper.toWebDTO(dto);
        DataResult<InstitutionChnlPymMthdPscWebDTO> resultDTO = new DataResult<>
                (ResultConstant.SUCCESSFULLY_ADDED.getMessage(), webDTO);
        return ResponseEntity.status(HttpStatus.CREATED).body(resultDTO);
    }

    @PutMapping("updateInstitutionChannelPymMethodPsc")
    public ResponseEntity<DataResult<InstitutionChnlPymMthdPscWebDTO>> updateInstitutionChannelPymMethodPsc(@RequestBody UpdateInstitutionChnlPymMthdPscRequest request) throws MicroException {
        UpdateInstitutionChnlPymMthdPscRequestDTO requestDTO=institutionChnnlPymMthdPscMapper.toRequestDTO(request);
        InstitutionChnnlPymMthdPscDTO dto = institutionChnlPymMthdPscService.updateInstitutionChnlPymMthdPsc(requestDTO);
        InstitutionChnlPymMthdPscWebDTO webDTO = institutionChnnlPymMthdPscMapper.toWebDTO(dto);
        DataResult<InstitutionChnlPymMthdPscWebDTO> resultDTO = new DataResult<>
                (ResultConstant.SUCCESSFULLY_UPDATED.getMessage(), webDTO);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }




}
