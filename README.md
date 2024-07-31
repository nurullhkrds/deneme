import com.ykb.payments.bill.transaction.payment.mapper.HarmoniMicroMapper;
import com.ykb.payments.bill.transaction.payment.web.request.RequestHarmoniQueryBills;
import com.ykb.payments.bill.transaction.payment.web.response.QueryBillsResponse;
import com.ykb.payments.bill.transaction.payment.web.response.ResponseHarmoniQueryBills;
import com.ykb.payments.bill.transaction.payment.web.response.SubsrciberNoPartResponseWebDTO;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mapstruct.factory.Mappers;
import org.mockito.InjectMocks;
import org.mockito.junit.jupiter.MockitoExtension;

import java.util.List;

import static org.junit.jupiter.api.Assertions.*;

@ExtendWith(MockitoExtension.class)
public class HarmoniMicroMapperTest {

    @InjectMocks
    private HarmoniMicroMapper mapper = Mappers.getMapper(HarmoniMicroMapper.class);

    @Test
    public void testToResponseHarmoniQueryBills() {
        QueryBillsResponse queryBillsResponse = new QueryBillsResponse();
        RequestHarmoniQueryBills harmoniRequest = new RequestHarmoniQueryBills();

        // Set necessary fields for queryBillsResponse
        queryBillsResponse.setSubscriberNoPartList(List.of(
                new SubsrciberNoPartResponseWebDTO(1, "1234"),
                new SubsrciberNoPartResponseWebDTO(2, "5678"),
                new SubsrciberNoPartResponseWebDTO(3, "91011")
        ));

        // Populate the bill list
        queryBillsResponse.setBillList(List.of(
                // Create and add necessary bill DTOs to the list
                new HmnBaseBillDTO()
        ));

        ResponseHarmoniQueryBills result = mapper.toResponseHarmoniQueryBills(queryBillsResponse, harmoniRequest);

        assertNotNull(result);
    }
}
