@Service
@RequiredArgsConstructor
public class ReceiptApiServiceImpl implements ReceiptApiService {


    private final ReceiptClientService receiptClientService;

    @Override
    public List<ResponseApiReceiptDTO> printReceipt(List<RequestApiReceiptDTO> request)   {
        return receiptClientService.printReceipt(request);

    }
}
