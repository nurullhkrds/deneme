@Service
@RequiredArgsConstructor
public class PaidBillLogService implements IPaidBillLogService {

    private final BillPaymentRestFacade facade;
    private final BillLogMapper mapper;
    private final RemoteServiceLogRepository repository;



    @Override
    public List<PaidBillLogDTO> getPaymentLogsByParameters(RequestPaidBillLogDTO requestDTO) {
        //micro ise gir içine ve işlemleri yapıp return et
        if (requestDTO.getIsMicro()){
            RequestPaidBillDTO billRequestDTO= mapper.toRequestDTO(requestDTO);
            List<PaidBillDTO> billDTOS=getPaymentsByParameters(billRequestDTO);

            if (!billDTOS.isEmpty()){
                

            }

        }
        // harmoni ise if bloğuna girmez harmoni servisini çağırır ve direk gelen yanıtı return eder.
        return facade.getPaymentLogsByParameters(requestDTO).getPaidBillLogList();
    }

    @Override
    public List<PaidBillDTO> getPaymentsByParameters(RequestPaidBillDTO requestDTO) {
        return facade.getPaymentsByParameters(requestDTO).getPaidBilList();
    }

    @Override
    public List<LogRecordDTO> getFilteredLogRecords(String institutionCode, String productCode, String returnMapCode, LocalDate startDate, LocalDate endDate) {
        return repository.findLogsByCriteria(institutionCode,productCode,returnMapCode,startDate,endDate);
    }
}
