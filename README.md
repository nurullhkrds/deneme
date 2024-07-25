package com.ykb.payments.bill.transaction.adapter.services.concretes;

import com.ykb.payments.bill.transaction.adapter.core.utilities.*;
import com.ykb.payments.bill.transaction.adapter.criteria.ReturnMapCriteria;
import com.ykb.payments.bill.transaction.adapter.domain.ReturnMap;
import com.ykb.payments.bill.transaction.adapter.dto.ReturnMapDTO;
import com.ykb.payments.bill.transaction.adapter.enums.EnumReturnType;
import com.ykb.payments.bill.transaction.adapter.mapper.ReturnMapMapper;
import com.ykb.payments.bill.transaction.adapter.repository.ReturnMapRepository;
import com.ykb.payments.bill.transaction.adapter.services.interfaces.IReturnMapService;
import com.ykb.payments.bill.transaction.adapter.web.request.CopyForIdsAndDataRequest;
import com.ykb.payments.bill.transaction.adapter.web.request.CreateReturnMapRequest;
import com.ykb.payments.bill.transaction.adapter.web.request.ReturnMapSearchCriteria;
import com.ykb.payments.bill.transaction.adapter.web.request.UpdateReturnMapRequest;
import lombok.RequiredArgsConstructor;
import org.springframework.data.domain.Sort;
import org.springframework.data.jpa.domain.Specification;
import org.springframework.data.redis.connection.ReturnType;
import org.springframework.http.HttpStatus;
import org.springframework.stereotype.Service;

import javax.transaction.Transactional;
import java.time.ZoneId;
import java.util.ArrayList;
import java.util.List;
import java.util.Objects;
import java.util.Optional;

@Service
@RequiredArgsConstructor
public class ReturnMapService implements IReturnMapService {
    private final ReturnMapRepository returnMapRepository;
    private final ReturnMapMapper returnMapMapper;



    @Override
    public DataResult<List<ReturnMapDTO>> getAll() {
        List<ReturnMap> returnMapList=returnMapRepository.findAll();
        List<ReturnMapDTO> returnMapDTOList = returnMapMapper.toReturnMapDTOList(returnMapList);
        boolean success = !returnMapDTOList.isEmpty();
        String message = success ? "Data retrieved successfully" : "No data found";
        int statusCode = success ? HttpStatus.OK.value() : HttpStatus.NOT_FOUND.value();

        return new DataResult<>(success, message, returnMapDTOList, statusCode);    }

    @Override
    public DataResult<List<ReturnMapDTO>> getAllByReturnMapCode(String returnMapCode) {
        List<ReturnMap> returnMapList = returnMapRepository.findByReturnMapCode(returnMapCode);
        List<ReturnMapDTO> returnMapDTOList = returnMapMapper.toReturnMapDTOList(returnMapList);

        boolean success = !returnMapDTOList.isEmpty();
        String message = success ? "Data retrieved successfully" : "No data found";
        int statusCode = success ? HttpStatus.OK.value() : HttpStatus.NOT_FOUND.value();

        return new DataResult<>(success, message, returnMapDTOList, statusCode);
    }

    @Transactional
    public DataResult<ReturnMapDTO> createReturnMap(CreateReturnMapRequest request) {
        ReturnMap returnMap = new ReturnMap();
        returnMap.setReturnMapCode(request.getReturnMapCode());
        returnMap.setInstitutionReturnCode(request.getInstitutionReturnCode());
        returnMap.setInstitutionReturnText(request.getInstitutionReturnText());
        returnMap.setBankReturnCode(request.getBankReturnCode());
        returnMap.setBankReturnText(request.getBankReturnText());
        returnMap.setIsReversible(request.getIsReversible());
        returnMap.setReturnType(Objects.equals(request.getReturnType(), "SUCCESS") ? EnumReturnType.SUCCESS:EnumReturnType.ERROR);
        ReturnMap result=returnMapRepository.save(returnMap);
        ReturnMapDTO resultDto=returnMapMapper.toReturnMapDTO(result);
        if (resultDto !=null){
            return new SuccessDataResult<>("ReturnMap Added",resultDto,200);
        }
        return new ErrorDataResult<>("ReturnMap not be added",null,400);
    }

    @Override
    public DataResult<ReturnMap> getReturnMapByIdSecond(Long returnMapCodeId) {
        Optional<ReturnMap> returnMapOptional=returnMapRepository.findById(returnMapCodeId);
        if (returnMapOptional.isPresent()){
            return new SuccessDataResult<>("result found",returnMapOptional.get(),200 );
        }
        return new ErrorDataResult<>("result not found !",null,400);    }

    @Transactional
    public DataResult<ReturnMapDTO> updateReturnMap(UpdateReturnMapRequest request) {
        DataResult<ReturnMap> returnMapDataResult = getReturnMapByIdSecond(request.getId());

        if (!returnMapDataResult.isSuccess()){
            return new ErrorDataResult<>("ReturnMap not be updated",null,400);
        }

        ReturnMap returnMap = returnMapDataResult.getData();

        returnMap.setReturnMapCode(request.getReturnMapCode());
        returnMap.setInstitutionReturnCode(request.getInstitutionReturnCode());
        returnMap.setInstitutionReturnText(request.getInstitutionReturnText());
        returnMap.setBankReturnCode(request.getBankReturnCode());
        returnMap.setBankReturnText(request.getBankReturnText());
        returnMap.setReturnType(Objects.equals(request.getReturnType(), "SUCCESS") ? EnumReturnType.SUCCESS:EnumReturnType.ERROR);
        returnMap.setIsReversible(request.getIsReversible());

        ReturnMap result=returnMapRepository.save(returnMap);
        ReturnMapDTO resultDto=returnMapMapper.toReturnMapDTO(result);

        if (resultDto !=null){
            return new SuccessDataResult<>("ReturnMap updated",resultDto,200);
        }
        return new ErrorDataResult<>("ReturnMap not be updated",null,400);
    }



    @Transactional
    public Result deleteReturnMaps(List<Long> ids) {
        try{
            List<ReturnMap> returnMapsToDelete = returnMapRepository.findAllById(ids);
            returnMapRepository.deleteAll(returnMapsToDelete);
            return new SuccessResult("ReturnMaps deleted",200);
        }
        catch (Exception e){
            return new ErrorResult("Error ! "+e.getMessage(),400);
        }
    }


    @Transactional
    public Result copyReturnMaps(CopyForIdsAndDataRequest request) {
        try {
            List<ReturnMap> returnMapsToCopy = returnMapRepository.findAllById(request.getIds());
            System.out.println("Found return maps: " + returnMapsToCopy.size());  // Debugging line

            List<ReturnMap> copiedReturnMaps = new ArrayList<>();

            for (ReturnMap original : returnMapsToCopy) {
                ReturnMap copied = new ReturnMap();
                copied.setReturnMapCode(request.getReturnMapCode());
                copied.setInstitutionReturnCode(original.getInstitutionReturnCode());
                copied.setInstitutionReturnText(original.getInstitutionReturnText());
                copied.setBankReturnCode(original.getBankReturnCode());
                copied.setBankReturnText(original.getBankReturnText());
                copied.setReturnType(original.getReturnType());
                copied.setIsReversible(original.getIsReversible());
                copiedReturnMaps.add(copied);
            }

            System.out.println("Copied return maps count: " + copiedReturnMaps.size());  // Debugging line

            returnMapRepository.saveAll(copiedReturnMaps);
            return new SuccessResult("copy successful", 200);

        } catch (Exception e) {
            return new ErrorResult("Error! " + e.getMessage(), 400);
        }
    }






    @Override
    public DataResult<ReturnMapDTO> getReturnMapById(Long returnMapCodeId) {
        Optional<ReturnMap> returnMapOptional=returnMapRepository.findById(returnMapCodeId);
        if (returnMapOptional.isPresent()){
            ReturnMapDTO dto=returnMapMapper.toReturnMapDTO(returnMapOptional.get());
            return new SuccessDataResult<>("result found",dto,200 );
        }
        return new ErrorDataResult<>("result not found !",null,400);
    }

    @Override
    public DataResult<List<ReturnMapDTO>> searchReturnMaps(String returnMapCode, String bankReturnCode, String institutionReturnCode) {
        Specification<ReturnMap> spec = Specification.where(null);

        if (returnMapCode != null && !returnMapCode.isEmpty()) {
            spec = spec.and(ReturnMapCriteria.hasReturnMapCode(returnMapCode));
        }
        if (bankReturnCode != null && !bankReturnCode.isEmpty()) {
            spec = spec.and(ReturnMapCriteria.hasBankErrorCode(bankReturnCode));
        }
        if (institutionReturnCode != null && !institutionReturnCode.isEmpty()) {
            spec = spec.and(ReturnMapCriteria.hasInstitutionErrorCode(institutionReturnCode));
        }

        List<ReturnMap> returnMapList = returnMapRepository.findAll(spec);
        List<ReturnMapDTO> returnMapDTOList = returnMapMapper.toReturnMapDTOList(returnMapList);

        if (returnMapDTOList.isEmpty()) {
            return new ErrorDataResult<>("Listed is empty", returnMapDTOList, 200);
        }

        return new SuccessDataResult<>("Result listed", returnMapDTOList, 200);
    }



    private boolean isNotBlank(String value) {
        return value != null && !value.trim().isEmpty();
    }


}
