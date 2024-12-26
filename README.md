@Hidden
@FeignClient(name = "PAYMENTS.BILL.bill-adapter-logger",path ="/api/v1/adapterLogs",configuration = { MicroErrorDecoder.class })
public interface AdapterLoggerServiceClient {

    @GetMapping(path = "/getPaymentLogsByParameters")
    List<RemoteServiceLogWebDTO> getPaymentLogsByParameters(
            @RequestParam Long institutionId,
            @RequestParam String serviceType,
            @RequestParam String startDate,
            @RequestParam String endDate);
}
