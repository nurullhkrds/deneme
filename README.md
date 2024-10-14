import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.*;

public class InstitutionChnnlPymMthdPscDTOTest {

    private InstitutionChnnlPymMthdPscDTO institutionChnnlPymMthdPscDTO;

    @BeforeEach
    public void setUp() {
        // InstitutionChnnlPymMthdPscDTO nesnesi oluşturuluyor ve örnek veriler atanıyor
        institutionChnnlPymMthdPscDTO = new InstitutionChnnlPymMthdPscDTO();
        institutionChnnlPymMthdPscDTO.setMondayBlockDayCount(1);
        institutionChnnlPymMthdPscDTO.setTuesdayBlockDayCount(2);
        institutionChnnlPymMthdPscDTO.setWednesdayBlockDayCount(3);
        institutionChnnlPymMthdPscDTO.setThursdayBlockDayCount(4);
        institutionChnnlPymMthdPscDTO.setFridayBlockDayCount(5);
        institutionChnnlPymMthdPscDTO.setSaturdayBlockDayCount(6);
        institutionChnnlPymMthdPscDTO.setSundayBlockDayCount(7);
    }

    @Test
    public void testGetBlockDayCountForMonday() {
        // Pazartesi (2) için blok gün sayısının doğru döndüğünü kontrol ediyoruz
        assertEquals(1, institutionChnnlPymMthdPscDTO.getBlockDayCount(2));
    }

    @Test
    public void testGetBlockDayCountForTuesday() {
        assertEquals(2, institutionChnnlPymMthdPscDTO.getBlockDayCount(3));
    }

    @Test
    public void testGetBlockDayCountForWednesday() {
        assertEquals(3, institutionChnnlPymMthdPscDTO.getBlockDayCount(4));
    }

    @Test
    public void testGetBlockDayCountForThursday() {
        assertEquals(4, institutionChnnlPymMthdPscDTO.getBlockDayCount(5));
    }

    @Test
    public void testGetBlockDayCountForFriday() {
        assertEquals(5, institutionChnnlPymMthdPscDTO.getBlockDayCount(6));
    }

    @Test
    public void testGetBlockDayCountForSaturday() {
        assertEquals(6, institutionChnnlPymMthdPscDTO.getBlockDayCount(7));
    }

    @Test
    public void testGetBlockDayCountForSunday() {
        assertEquals(7, institutionChnnlPymMthdPscDTO.getBlockDayCount(1));
    }

    @Test
    public void testGetBlockDayCountForInvalidDay() {
        // Geçersiz bir gün değeri gönderildiğinde 0 döndüğünü kontrol ediyoruz
        assertEquals(0, institutionChnnlPymMthdPscDTO.getBlockDayCount(8));
        assertEquals(0, institutionChnnlPymMthdPscDTO.getBlockDayCount(-1));
    }
}
