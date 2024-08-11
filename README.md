public class ProcessServiceImplTest {

    @Mock
    private ProcessChannelService processChannelService;

    @Mock
    private InstitutionService institutionService;

    @Mock
    private InstitutionDebtTypeService institutionDebtTypeService;

    @InjectMocks
    private ProcessServiceImpl processServiceImpl;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
        CacheManager cacheManager = new ConcurrentMapCacheManager();
    }

    @Test
    void testGetProcessChannel_ValidInput() {
        ProcessChannelDTO expectedDto = new ProcessChannelDTO();
        when(processChannelService.findProcessChannel(anyString(), anyString())).thenReturn(expectedDto);

        ProcessChannelDTO result = processServiceImpl.getProcessChannel("code", "channelCode");

        assertEquals(expectedDto, result);
    }

    @Test
    void testGetProcessChannel_EmptyCodeOrChannelCode() {
        ProcessChannelDTO result1 = processServiceImpl.getProcessChannel("", "channelCode");
        ProcessChannelDTO result2 = processServiceImpl.getProcessChannel("code", "");

        assertNull(result1);
        assertNull(result2);
    }

    @Test
    void testGetInstitutionDebtTypeForProcess_WithDebtTypeId() {
        InstitutionDebtTypeDTO expectedDto = new InstitutionDebtTypeDTO();
        when(institutionDebtTypeService.getDebtType(anyLong())).thenReturn(expectedDto);

        InstitutionDebtTypeDTO result = processServiceImpl.getInstitutionDebtTypeForProcess("productCode", "institutionCode", 1L);

        assertEquals(expectedDto, result);
    }

    @Test
    void testGetInstitutionDebtTypeForProcess_WithProductAndInstitutionCode() {
        InstitutionDebtTypeDTO expectedDto = new InstitutionDebtTypeDTO();
        when(institutionDebtTypeService.getDefaultDebtType(anyString(), anyString())).thenReturn(expectedDto);

        InstitutionDebtTypeDTO result = processServiceImpl.getInstitutionDebtTypeForProcess("productCode", "institutionCode", null);

        assertEquals(expectedDto, result);
    }

    @Test
    void testGetInstitutionDebtTypeForProcess_EmptyProductOrInstitutionCode() {
        InstitutionDebtTypeDTO result = processServiceImpl.getInstitutionDebtTypeForProcess("", "", null);

        assertNull(result);
    }

    @Test
    void testGetInstitutionDebtTypeForProcess_NullDebtTypeIdAndCodes() {
        InstitutionDebtTypeDTO result = processServiceImpl.getInstitutionDebtTypeForProcess(null, null, null);

        assertNull(result);
    }
