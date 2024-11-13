package com.ykb.payments.bill.transaction.institution.admin.web;


import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.transaction.adapter.constant.ResultConstant;
import com.ykb.payments.bill.transaction.adapter.core.utilities.DataResult;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionChannelProcessMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionChnlProccesService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionChannelProcessRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionChannelProcessRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.request.create.CreateInstitutionChannelProcessRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.request.update.UpdateInstitutionChannelProcessRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.response.InstitutionChnlProcessWebDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionChannelProcessDTO;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/admin/InstitutionProcess")
public class AdminInstitutionChnlProccesController {

    private final AdminInstitutionChnlProccesService institutionChnlProccesService;

    private final AdminInstitutionChannelProcessMapper institutionChannelProcessMapper;


    public AdminInstitutionChnlProccesController(AdminInstitutionChnlProccesService institutionChnlProccesService, AdminInstitutionChannelProcessMapper institutionChannelProcessMapper) {
        this.institutionChnlProccesService = institutionChnlProccesService;
        this.institutionChannelProcessMapper = institutionChannelProcessMapper;
    }




    @GetMapping("getAllInstitutionChnlProcceses")
    public ResponseEntity<DataResult<List<InstitutionChnlProcessWebDTO>>> getAllInstitutionChnlProcceses(){
        List<InstitutionChnlProcessWebDTO> webDTOList= institutionChnlProccesService.getAllInstitutionChnlProcceses();
        DataResult<List<InstitutionChnlProcessWebDTO>> resultDTO=
                new DataResult<>(ResultConstant.DATA_LISTED.getMessage(),webDTOList);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);

    }


    @GetMapping("getInstitutionChannelProccesById")
    public ResponseEntity<DataResult<InstitutionChnlProcessWebDTO>> getInstitutionChannelProccesById(@RequestParam Long id){
        InstitutionChannelProcessDTO dto= institutionChnlProccesService.getInstitutionChannelProccesById(id);
        InstitutionChnlProcessWebDTO webDTO = institutionChannelProcessMapper.toWebDTO(dto);
        DataResult<InstitutionChnlProcessWebDTO> resultDTO=
                new DataResult<>(ResultConstant.DATA_RETRIEVED.getMessage(),webDTO);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }


    @PostMapping("createInstitutionChannelProcces")
    public ResponseEntity<DataResult<InstitutionChnlProcessWebDTO>> createInstitutionChannelProcces
            (@RequestBody CreateInstitutionChannelProcessRequest request) throws MicroException
    {
        CreateInstitutionChannelProcessRequestDTO requestDTO=
                institutionChannelProcessMapper.toRequestDTO(request);
        InstitutionChannelProcessDTO dto= institutionChnlProccesService.createInstitutionChannelProcces(requestDTO);
        InstitutionChnlProcessWebDTO webDTO = institutionChannelProcessMapper.toWebDTO(dto);
        DataResult<InstitutionChnlProcessWebDTO> resultDTO=
                new DataResult<>(ResultConstant.SUCCESSFULLY_ADDED.getMessage(),webDTO);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }

    @PutMapping("updateInstitutionChannelProcces")
    public ResponseEntity<DataResult<InstitutionChnlProcessWebDTO>> updateInstitutionChannelProcces
            (@RequestBody UpdateInstitutionChannelProcessRequest request) throws MicroException{
        UpdateInstitutionChannelProcessRequestDTO requestDTO=
                institutionChannelProcessMapper.toRequestDTO(request);
        InstitutionChannelProcessDTO dto= institutionChnlProccesService.updateInstitutionChannelProcces(requestDTO);
        InstitutionChnlProcessWebDTO webDTO = institutionChannelProcessMapper.toWebDTO(dto);
        DataResult<InstitutionChnlProcessWebDTO> resultDTO=
                new DataResult<>(ResultConstant.SUCCESSFULLY_UPDATED.getMessage(),webDTO);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }




}
