package com.ykb.payments.bill.transaction.adapter.mapper;


import com.ykb.payments.bill.transaction.adapter.dto.LogRecordDTO;
import com.ykb.payments.bill.transaction.adapter.web.request.billLog.PaidBillLogRequest;
import com.ykb.payments.bill.transaction.adapter.web.response.PaidBillLogWebDTO;
import com.ykb.payments.bill.transaction.external.harmoni.billpayment.rest.dto.PaidBillLogDTO;
import com.ykb.payments.bill.transaction.external.harmoni.billpayment.rest.request.RequestPaidBillDTO;
import com.ykb.payments.bill.transaction.external.harmoni.billpayment.rest.request.RequestPaidBillLogDTO;
import org.mapstruct.Mapper;

import java.time.LocalDate;
import java.util.List;

@Mapper(componentModel = "spring")
public interface BillLogMapper {


    RequestPaidBillLogDTO toRequestDTO(PaidBillLogRequest request);
    RequestPaidBillDTO toRequestDTO(RequestPaidBillLogDTO requestDTO);

    PaidBillLogWebDTO toWebDTO(PaidBillLogDTO dto);

    List<PaidBillLogWebDTO> toWebDTOList(List<PaidBillLogDTO> dtoList);


     LogRecordDTO mapToLogRecordDTO(Object[] row) {
        LogRecordDTO dto = new LogRecordDTO();

        // Sorgudan gelen sütunları sırasıyla set et
        dto.setSubscriberNo((String) row[0]);
        dto.setLogDate(convertToLocalDate(row[1]));
        dto.setReceivedData((String) row[2]);
        dto.setSendData((String) row[3]);
        dto.setInstitutionReturnCode((String) row[4]);
        dto.setReturnMapCode((String) row[5]);
        dto.setInstitutionReturnText((String) row[6]);
        dto.setBankReturnCode((String) row[7]);
        dto.setInstitutionCode((String) row[8]);
        dto.setProductCode((String) row[9]);

        return dto;
    }

    private LocalDate convertToLocalDate(Object date) {
        if (date instanceof Timestamp) {
            return ((Timestamp) date).toLocalDateTime().toLocalDate();
        }
        return null;
    }


}
