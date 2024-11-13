package com.ykb.payments.bill.transaction.institution.admin.web;

import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.transaction.adapter.constant.ResultConstant;
import com.ykb.payments.bill.transaction.adapter.core.utilities.DataResult;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionChannelMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionChannelService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionChannelRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionChannelRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.request.create.CreateInstitutionChannelRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.request.update.UpdateInstitutionChannelRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.response.InstitutionChannelWebDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionChannelDTO;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/admin/institutionChannels")
public class AdminInstitutionChannelController {

    private final AdminInstitutionChannelService institutionChannelService;
    private final AdminInstitutionChannelMapper institutionChannelMapper;

    public AdminInstitutionChannelController(AdminInstitutionChannelService institutionChannelService, AdminInstitutionChannelMapper institutionChannelMapper) {
        this.institutionChannelService = institutionChannelService;
        this.institutionChannelMapper = institutionChannelMapper;
    }


    @GetMapping("getAllInstitutionChannels")
    public ResponseEntity<DataResult<List<InstitutionChannelWebDTO>>> getAllInstitutionChannels() {
        List<InstitutionChannelDTO> institutionChannelDTOS = institutionChannelService.getAllInstitutionChannels();
        List<InstitutionChannelWebDTO> institutionChannelWebDTOList = institutionChannelMapper.toWebDTOList(institutionChannelDTOS);
        DataResult<List<InstitutionChannelWebDTO>> resultDTO = new DataResult<>
                (ResultConstant.DATA_LISTED.getMessage(), institutionChannelWebDTOList);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);

    }


    @GetMapping("getInstitutionChannelById")
    public ResponseEntity<DataResult<InstitutionChannelWebDTO>> getInstitutionChannelById(@RequestParam Long id) {
        InstitutionChannelDTO institutionChannelDTO = institutionChannelService.getInstitutionChannelById( id);
        InstitutionChannelWebDTO institutionChannelWebDTO = institutionChannelMapper.toWebDTO(institutionChannelDTO);
        DataResult<InstitutionChannelWebDTO> resultDTO = new DataResult<>(ResultConstant.DATA_RETRIEVED.getMessage(), institutionChannelWebDTO);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }

    @PostMapping("createInstitutionChannel")
    public ResponseEntity<DataResult<InstitutionChannelWebDTO>> createInstitutionChannel(@RequestBody CreateInstitutionChannelRequest request) throws MicroException {
        CreateInstitutionChannelRequestDTO requestDTO = institutionChannelMapper.toRequestDTO(request);
        InstitutionChannelDTO institutionChannelDTO = institutionChannelService.createInstitutionChannel(requestDTO);
        InstitutionChannelWebDTO institutionChannelWebDTO = institutionChannelMapper.toWebDTO(institutionChannelDTO);
        DataResult<InstitutionChannelWebDTO> resultDTO = new DataResult<>(ResultConstant.INSTITUTION_CHANNEL_CREATED.getMessage(), institutionChannelWebDTO);
        return ResponseEntity.status(HttpStatus.CREATED).body(resultDTO);
    }

    @PutMapping("updateInstitutionChannel")
    public ResponseEntity<DataResult<InstitutionChannelWebDTO>> updateInstitutionChannel(@RequestBody UpdateInstitutionChannelRequest request) throws MicroException {
        UpdateInstitutionChannelRequestDTO requestDTO = institutionChannelMapper.toRequestDTO(request);
        InstitutionChannelDTO institutionChannelDTO = institutionChannelService.updateInstitutionChannel(requestDTO);
        InstitutionChannelWebDTO institutionChannelWebDTO = institutionChannelMapper.toWebDTO(institutionChannelDTO);
        DataResult<InstitutionChannelWebDTO> resultDTO = new DataResult<>(ResultConstant.INSTITUTION_CHANNEL_UPDATED.getMessage(), institutionChannelWebDTO);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }
}
