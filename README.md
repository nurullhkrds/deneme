@Service
@RequiredArgsConstructor
public class AccountingDataLookupServiceImpl implements  AccountingDataLookupService{

    private final AccountingDataLookUpServiceClient  accountingDataLookUpServiceClient;


    @Override
    public Date getNextBusinessDate(Date firstDate, Integer workDayCount) {
        RequestGetGlobalNextBusinessDate requestGetGlobalNextBusinessDate = new RequestGetGlobalNextBusinessDate();
        requestGetGlobalNextBusinessDate.setFirstDate(firstDate);
        requestGetGlobalNextBusinessDate.setWorkDayCount(workDayCount);
        requestGetGlobalNextBusinessDate.setCountry(EnumCountry.TURKEY.getCode());
        ResponseGetGlobalNextBusinessDate responseGetGlobalNextBusinessDate = accountingDataLookUpServiceClient.getGlobalNextBusinessDate(requestGetGlobalNextBusinessDate);
        return responseGetGlobalNextBusinessDate.getRealDate();
    }
}
