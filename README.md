package com.ykb.payments.bill.transaction.institution.admin.web;


import com.ykb.architecture.micro.error.exception.MicroException;
import com.ykb.payments.bill.transaction.adapter.constant.ResultConstant;
import com.ykb.payments.bill.transaction.adapter.core.utilities.DataResult;
import com.ykb.payments.bill.transaction.institution.admin.mapper.AdminInstitutionCityMapper;
import com.ykb.payments.bill.transaction.institution.admin.service.intf.AdminInstitutionCityService;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.create.CreateInstitutionCityRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.dto.update.UpdateInstitutionCityRequestDTO;
import com.ykb.payments.bill.transaction.institution.admin.web.request.create.CreateInstitutionCityRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.request.update.UpdateInstitutionCityRequest;
import com.ykb.payments.bill.transaction.institution.admin.web.response.InstitutionCityWebDTO;
import com.ykb.payments.bill.transaction.institution.dto.InstitutionCityDTO;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import javax.validation.Valid;
import java.util.List;

@RestController
@RequestMapping("/admin/institutionCities")
public class AdminInsitutionCityController {

    private final AdminInstitutionCityService institutionCityService;
    private final AdminInstitutionCityMapper institutionCityMapper;

    public AdminInsitutionCityController(AdminInstitutionCityService institutionCityService, AdminInstitutionCityMapper institutionCityMapper) {
        this.institutionCityService = institutionCityService;
        this.institutionCityMapper = institutionCityMapper;
    }

    @GetMapping("getAllInstitutionCities")
    public ResponseEntity<DataResult<List<InstitutionCityWebDTO>>> getAllInstitutionCities() {
        List<InstitutionCityDTO> institutionCityDTOS = institutionCityService.getAllInstitutionCities();
        List<InstitutionCityWebDTO> institutionCityWebDTOList = institutionCityMapper.toWebDTOList(institutionCityDTOS);
        DataResult<List<InstitutionCityWebDTO>> resultDTO = new DataResult<>
                (ResultConstant.DATA_LISTED.getMessage(), institutionCityWebDTOList);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }


    @GetMapping("getInstitutionCityById")
    public ResponseEntity<DataResult<InstitutionCityWebDTO>> getInstitutionCityById(@RequestParam Long institutionCityId) {
        InstitutionCityDTO institutionCityDTO = institutionCityService.getInstitutionCityById(institutionCityId);
        InstitutionCityWebDTO institutionCityWebDTO = institutionCityMapper.toWebDTO(institutionCityDTO);
        DataResult<InstitutionCityWebDTO> resultDTO = new DataResult<>(ResultConstant.DATA_RETRIEVED.getMessage(), institutionCityWebDTO);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }

    @PostMapping("createInstitutionCity")
    public ResponseEntity<DataResult<InstitutionCityWebDTO>> createInstitutionCity(@RequestBody @Valid CreateInstitutionCityRequest request) throws MicroException {
        CreateInstitutionCityRequestDTO requestDTO = institutionCityMapper.toCreateDTO(request);
        InstitutionCityDTO institutionCityDTO = institutionCityService.createInstitutionCity(requestDTO);
        InstitutionCityWebDTO webDTO = institutionCityMapper.toWebDTO(institutionCityDTO);
        DataResult<InstitutionCityWebDTO> resultDTO = new DataResult<>(ResultConstant.INSTITUTION_CITY_CREATED.getMessage(), webDTO);
        return ResponseEntity.status(HttpStatus.CREATED).body(resultDTO);
    }

    @PutMapping("updateInstitutionCity")
    public ResponseEntity<DataResult<InstitutionCityWebDTO>> updateInstitutionCity(@RequestBody @Valid UpdateInstitutionCityRequest request) throws MicroException {
        UpdateInstitutionCityRequestDTO requestDTO = institutionCityMapper.toUpdateDTO(request);
        InstitutionCityDTO institutionCityDTO = institutionCityService.updateInstitutionCity(requestDTO);
        InstitutionCityWebDTO webDTO = institutionCityMapper.toWebDTO(institutionCityDTO);
        DataResult<InstitutionCityWebDTO> resultDTO = new DataResult<>(ResultConstant.INSTITUTION_CITY_UPDATED.getMessage(), webDTO);
        return ResponseEntity.status(HttpStatus.OK).body(resultDTO);
    }
}
