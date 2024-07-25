import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.mockito.junit.MockitoJUnitRunner;

import java.math.BigDecimal;
import java.time.LocalDate;
import java.time.LocalTime;
import java.util.Arrays;
import java.util.List;

import static org.mockito.Mockito.*;
import static org.junit.Assert.*;

@RunWith(MockitoJUnitRunner.class)
public class BillPaymentProcessTest {

    @InjectMocks
    private BillPaymentProcess billPaymentProcess;

    @Mock
    private ProvisionService provisionService;
    @Mock
    private AccountingService accountingService;
    @Mock
    private InstitutionChannelPymMethodService institutionChannelPymMethodService;
    @Mock
    private InstitutionChnnlPymMthdAccService institutionChnnlPymMthdAccService;
    @Mock
    private InstitutionChnnlPymMthdPscService institutionChnnlPymMthdPscService;
    @Mock
    private PaymentService paymentService;
    @Mock
    private PaymentNotificationService paymentNotificationService;
    @Mock
    private LimitationService limitationService;
    @Mock
    private PaymentEventPublisher paymentEventPublisher;
    @Mock
    private InstitutionAccountingInfoService institutionAccountingInfoService;
    @Mock
    private PaymentUtilImpl paymentUtilImpl;

    @Before
    public void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    public void testGatherDataStep() {
        // Arrange
        BillPaymentProcess.GatherData gatherData = billPaymentProcess.new GatherData();
        when(dataPack.get(ProcessDataPackKey.BILL_PROVISION_ID.getKey())).thenReturn(123L);
        when(dataPack.get(ProcessDataPackKey.PAYMENT_AMOUNT.getKey())).thenReturn(new BigDecimal("100.00"));
        when(dataPack.get(ProcessDataPackKey.CURRENCY.getKey())).thenReturn("USD");
        when(SpringUtil.getBean(PaymentNotificationService.class)).thenReturn(paymentNotificationService);
        when(SpringUtil.getBean(PaymentEventPublisher.class)).thenReturn(paymentEventPublisher);
        when(dataPack.get(ProcessDataPackKey.PAYMENT_METHOD_TYPE.getKey())).thenReturn(EnumPaymentMethod.ACCOUNT);
        when(dataPack.get(ProcessDataPackKey.ACCOUNT_PAYMENT_METHOD_DETAIL.getKey())).thenReturn(new PaymentMethodDetailDTO());

        // Act
        gatherData.executeStep();

        // Assert
        assertEquals(Long.valueOf(123L), billPaymentProcess.getBillProvisionId());
        assertEquals(new BigDecimal("100.00"), billPaymentProcess.getPaymentAmount());
        assertEquals(EnumCurrencyCode.USD, billPaymentProcess.getCurrency());
        assertNotNull(billPaymentProcess.getPaymentNotificationService());
        assertNotNull(billPaymentProcess.getPaymentEventPublisher());
        assertEquals(EnumPaymentMethod.ACCOUNT, billPaymentProcess.getPaymentMethodType());
        assertNotNull(billPaymentProcess.getPaymentMethodDetailDTO());
    }

    @Test
    public void testFetchProvisionRecordStep() {
        // Arrange
        BillPaymentProcess.FetchProvisionRecord fetchProvisionRecord = billPaymentProcess.new FetchProvisionRecord();
        ProvisionDTO provisionDTO = new ProvisionDTO();
        provisionDTO.setStatus(EnumProvisionStatus.NOT_PAID);
        provisionDTO.setProvisionDate(LocalDate.now());

        when(provisionService.getProvisionRecord(anyLong())).thenReturn(provisionDTO);

        // Act
        fetchProvisionRecord.executeStep();

        // Assert
        assertNotNull(billPaymentProcess.getProvisionDTO());
        assertEquals(EnumProvisionStatus.NOT_PAID, billPaymentProcess.getProvisionDTO().getStatus());
    }

    @Test
    public void testDoAccountingStep() {
        // Arrange
        BillPaymentProcess.DoAccounting doAccounting = billPaymentProcess.new DoAccounting();
        CreateAccountingResultDTO accountingResultDTO = new CreateAccountingResultDTO();
        accountingResultDTO.setSuccess(true);
        when(accountingService.doAccounting(any(CreateAccountingDTO.class))).thenReturn(accountingResultDTO);

        // Act
        doAccounting.executeStep();

        // Assert
        assertNotNull(billPaymentProcess.getCreateAccountingResultDTO());
        assertTrue(billPaymentProcess.getCreateAccountingResultDTO().isSuccess());
    }

    @Test
    public void testFetchInstitutionPaymentMethodAccStep() {
        // Arrange
        BillPaymentProcess.FetchInstitutionPaymentMethodAcc fetchInstitutionPaymentMethodAcc = billPaymentProcess.new FetchInstitutionPaymentMethodAcc();
        InstitutionChannelPymMethodDTO institutionChannelPymMethodDTO = new InstitutionChannelPymMethodDTO();
        institutionChannelPymMethodDTO.setIsActive(true);
        InstitutionChnnlPymMthdAccDTO institutionChnnlPymMthdAccDTO = new InstitutionChnnlPymMthdAccDTO();
        institutionChnnlPymMthdAccDTO.setIsActive(true);

        when(institutionChannelPymMethodService.getInstitutionPymMethodByChannelId(anyLong(), any(EnumPaymentMethod.class)))
                .thenReturn(institutionChannelPymMethodDTO);
        when(institutionChnnlPymMthdAccService.getInstitutionChnnlPymMthdAcc(anyLong(), anyString()))
                .thenReturn(institutionChnnlPymMthdAccDTO);
        when(institutionAccountingInfoService.getInstitutionAccountingInfoByInstitutionId(anyLong()))
                .thenReturn(new InstitutionAccountingInfoDTO());

        // Act
        fetchInstitutionPaymentMethodAcc.executeStep();

        // Assert
        assertNotNull(billPaymentProcess.getInstitutionChannelPymMethodDTO());
        assertTrue(billPaymentProcess.getInstitutionChannelPymMethodDTO().getIsActive());
        assertNotNull(billPaymentProcess.getInstitutionChnnlPymMthdAccDTO());
        assertTrue(billPaymentProcess.getInstitutionChnnlPymMthdAccDTO().getIsActive());
    }

    @Test
    public void testCheckPartialAndOverPaymentStep() {
        // Arrange
        BillPaymentProcess.CheckPartialAndOverPayment checkPartialAndOverPayment = billPaymentProcess.new CheckPartialAndOverPayment();
        ProvisionDTO provisionDTO = new ProvisionDTO();
        provisionDTO.setAmount(new BigDecimal("100.00"));
        billPaymentProcess.setProvisionDTO(provisionDTO);
        billPaymentProcess.setPaymentAmount(new BigDecimal("50.00"));

        InstitutionChannel institutionChannel = new InstitutionChannel();
        institutionChannel.setIsPartialPaymentAllowed(true);
        billPaymentProcess.setInstitutionChannel(institutionChannel);

        // Act
        checkPartialAndOverPayment.executeStep();

        // Assert
        assertNull(billPaymentProcess.getError());
    }

    @Test
    public void testCheckPaymentAmountStep() {
        // Arrange
        BillPaymentProcess.CheckPaymentAmount checkPaymentAmount = billPaymentProcess.new CheckPaymentAmount();
        billPaymentProcess.setPaymentAmount(new BigDecimal("100.00"));

        // Act
        checkPaymentAmount.executeStep();

        // Assert
        assertNull(billPaymentProcess.getError());
    }

    @Test
    public void testUpdateProvisionStep() {
        // Arrange
        BillPaymentProcess.UpdateProvision updateProvision = billPaymentProcess.new UpdateProvision();
        ProvisionDTO provisionDTO = new ProvisionDTO();
        billPaymentProcess.setProvisionDTO(provisionDTO);

        // Act
        updateProvision.executeStep();

        // Assert
        assertEquals(EnumProvisionStatus.PAID, billPaymentProcess.getProvisionDTO().getStatus());
        verify(provisionService, times(1)).updateProvision(provisionDTO);
    }

    @Test
    public void testInsertPaymentRecordStep() {
        // Arrange
        BillPaymentProcess.InsertPaymentRecord insertPaymentRecord = billPaymentProcess.new InsertPaymentRecord();
        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();
        createAccountingResultDTO.setContractNo("123456");
        createAccountingResultDTO.setCommissionAmount(new BigDecimal("5.00"));
        billPaymentProcess.setCreateAccountingResultDTO(createAccountingResultDTO);
        billPaymentProcess.setProvisionDTO(new ProvisionDTO());
        billPaymentProcess.setInstitution(new Institution());
        billPaymentProcess.setInstitutionDebtType(new InstitutionDebtType());
        billPaymentProcess.setCurrency(EnumCurrencyCode.USD);

        // Act
        insertPaymentRecord.executeStep();

        // Assert
        assertNotNull(billPaymentProcess.getPaymentDTO());
        verify(paymentService, times(1)).insertPayment(billPaymentProcess.getPaymentDTO());
    }

    @Test
    public void testInsertPaymentNotificationRecordStep() {
        // Arrange
        BillPaymentProcess.InsertPaymentNotificationRecord insertPaymentNotificationRecord = billPaymentProcess.new InsertPaymentNotificationRecord();
        billPaymentProcess.setPaymentDTO(new PaymentDTO());

        // Act
        insertPaymentNotificationRecord.executeStep();

        // Assert
        assertNotNull(billPaymentProcess.getInsertedPaymentNotificationDTOList());
        verify(paymentNotificationService, times(1)).insertPaymentNotification(any(PaymentNotificationDTO.class));
    }

    @Test
    public void testPublishBillPaymentEventStep() {
        // Arrange
        BillPaymentProcess.PublishBillPaymentEvent publishBillPaymentEvent = billPaymentProcess.new PublishBillPaymentEvent();
        billPaymentProcess.setPaymentDTO(new PaymentDTO());
        billPaymentProcess.setInstitution(new Institution());
        billPaymentProcess.setInsertedPaymentNotificationDTOList(Arrays.asList(new PaymentNotificationDTO()));

        // Act
        publishBillPaymentEvent.executeStep();

        // Assert
        verify(paymentEventPublisher, times(1)).findPublishPaymentEvent(any(PublishPaymentTypeDTO.class));
    }

    @Test
    public void testUpdateCustomerPaymentLimitStep() {
        // Arrange
        BillPaymentProcess.UpdateCustomerPaymentLimit updateCustomerPaymentLimit = billPaymentProcess.new UpdateCustomerPaymentLimit();
        ProvisionDTO provisionDTO = new ProvisionDTO();
        provisionDTO.setCustomerNo(123L);
        provisionDTO.setIdentityNo(456L);
        billPaymentProcess.setProvisionDTO(provisionDTO);
        CreateAccountingResultDTO createAccountingResultDTO = new CreateAccountingResultDTO();
        createAccountingResultDTO.setContractNo("123456");
        billPaymentProcess.setCreateAccountingResultDTO(createAccountingResultDTO);
        billPaymentProcess.setFomOperationEnabled(true);

        // Act
        updateCustomerPaymentLimit.executeStep();

        // Assert
        verify(limitationService, times(1)).isPaymentAllowedWithoutDebtOwner(anyString(), anyInt(), anyString(), anyString());
    }
}
