import java.util.List;
import java.util.stream.Collectors;

public class RequestTransformationService {

    public FinalRequestDTO transformToFinalRequest(InitialRequestDTO initialRequest) {
        FinalRequestDTO finalRequest = new FinalRequestDTO();

        if (initialRequest.getChannelCodes() == null || initialRequest.getChannelCodes().isEmpty()) {
            finalRequest.setPaymentTypeList(new String[0]); // Boş ise boş dizi
            return finalRequest;
        }

        // Kanal kodlarını Enum'dan code değerine çevir
        String[] paymentTypeList = initialRequest.getChannelCodes().stream()
                .map(EnumOldChannels::parseValue) // Enum eşleşmesini bul
                .filter(channel -> channel != null && channel.getCode() != null) // Geçerli olanları al
                .map(EnumOldChannels::getCode) // Code değerini al
                .toArray(String[]::new); // String[]'e dönüştür

        // İkinci request DTO'suna set et
        finalRequest.setPaymentTypeList(paymentTypeList);
        return finalRequest;
    }
}
