public class BaseLogDTOTest {

    private BaseLogDTO baseLogDTO;

    @BeforeEach
    void setUp() {
        // Anonim sınıf oluşturuyoruz
        baseLogDTO = new BaseLogDTO() {
            // Gerekirse anonim sınıfın metotlarını override edebilirsiniz
        };
    }

    @Test
    void testDefaultValues() {
        // Başlangıçta set edilen değerleri kontrol ediyoruz
        assertNotNull(baseLogDTO);
        assertEquals(System.currentTimeMillis(), baseLogDTO.getStartTime(), 1000); // 1000ms tolerans
        assertEquals(baseLogDTO.getStartTime(), baseLogDTO.getLastLoggingTime());
        assertEquals(0L, baseLogDTO.getFinishTime());
        assertNull(baseLogDTO.getException());
    }

    @Test
    void testSettersAndGetters() {
        // Değerleri set ediyoruz
        baseLogDTO.setInstitutionId(1L);
        baseLogDTO.setInstitutionDebtTypeId(2L);
        baseLogDTO.setSubscriberNo("123456");
        baseLogDTO.setLogDate(LocalDate.now());
        baseLogDTO.setBranchCode("BR001");
        baseLogDTO.setChannelCode("CH001");
        baseLogDTO.setChannelTransactionId("TX123");
        baseLogDTO.setChannelSessionId("SESS123");
        baseLogDTO.setElapsedTime(3000L);
        baseLogDTO.setException(new RuntimeException("Test Exception"));

        // Değerleri kontrol ediyoruz
        assertEquals(1L, baseLogDTO.getInstitutionId());
        assertEquals(2L, baseLogDTO.getInstitutionDebtTypeId());
        assertEquals("123456", baseLogDTO.getSubscriberNo());
        assertEquals(LocalDate.now(), baseLogDTO.getLogDate());
        assertEquals("BR001", baseLogDTO.getBranchCode());
        assertEquals("CH001", baseLogDTO.getChannelCode());
        assertEquals("TX123", baseLogDTO.getChannelTransactionId());
        assertEquals("SESS123", baseLogDTO.getChannelSessionId());
        assertEquals(3000L, baseLogDTO.getElapsedTime());
        assertEquals("Test Exception", baseLogDTO.getException().getMessage());
    }

    @Test
    void testTimeUpdates() {
        long newLastLoggingTime = System.currentTimeMillis() + 1000;
        baseLogDTO.setLastLoggingTime(newLastLoggingTime);
        assertEquals(newLastLoggingTime, baseLogDTO.getLastLoggingTime());

        long newFinishTime = System.currentTimeMillis() + 2000;
        baseLogDTO.setFinishTime(newFinishTime);
        assertEquals(newFinishTime, baseLogDTO.getFinishTime());
    }
}
