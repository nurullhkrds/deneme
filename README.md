@RestController
@RequestMapping("/api/v1/adapterLogs")
@RequiredArgsConstructor
public class AdapterLogController {

    private final RemoteServiceLogRepository repository;

    @GetMapping("/deneme")
    public ResponseEntity<List<RemoteServiceLog>> getPaymentLogsByParameters(
            RemoteServiceLogRequest request){

        return ResponseEntity.status(HttpStatus.OK).body(repository
                .findAllByInstitutionIdAndServiceTypeAndLogDateBetween(request.getInstitutionId(), request.getServiceType(), request.getStartDate(), request.getEndDate()));
    }



}
