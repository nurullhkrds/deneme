import static org.mockito.Mockito.*;

import java.time.LocalDateTime;
import java.util.Optional;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;
import org.springframework.context.ApplicationContext;

public class NotifyPaymentProcessTest {

    @Mock
    private PaymentService paymentService;

    @Mock
    private PaymentNotificationService paymentNotificationService;

    @Mock
    private AdapterService adapterService;

    @InjectMocks
    private NotifyPaymentProcess process;

    @Mock
    private ProcessService processService;

    @Mock
    private ApplicationContext applicationContext;

    @Mock
    private InstitutionFeatureService institutionFeatureService;

    @BeforeEach
    void setUp() {
        MockitoAnnotations.openMocks(this);
    }

    @Test
    void testExecuteProcessSuccess() throws BillException {
        SpringUtil springUtil = new SpringUtil();
        springUtil.setApplicationContext(applicationContext);

        when(getBean(ProcessService.class)).thenReturn(processService);
        when(getBean(InstitutionFeatureService.class)).thenReturn(institutionFeatureService);
        when(getBean(PaymentNotificationService.class)).thenReturn(paymentNotificationService);
        when(getBean(PaymentService.class)).thenReturn(paymentService);
        when(getBean(AdapterService.class)).thenReturn(adapterService);

        PaymentNotification mockNotification = new PaymentNotification();
        mockNotification.setNotificationType(EnumPaymentNotificationType.INSTITUTION_PAYMENT_NOTIFICATION);
        mockNotification.setNotificationStatus(EnumPaymentNotificationStatu.WAITING);
        mockNotification.setPaymentId(1L);
        mockNotification.setRetryCount(0);

        Payment mockPayment = new Payment();
        mockPayment.setInstitutionDebtTypeId(1L);
        mockPayment.setCreateDate(LocalDateTime.now());
        mockPayment.setAdditionalInfo1("Info1");

        NotifyPaymentAdapterResponse mockResponse = new NotifyPaymentAdapterResponse();
        mockResponse.setStatus(BillPaymentsConsts.RESPONSE_STATUS.SUCCESS);
        mockResponse.setNotifiedBill(new NotifiedBillAdapterDTO());

        InstitutionDebtTypeDTO institutionDebtTypeDTO = new InstitutionDebtTypeDTO();
        institutionDebtTypeDTO.setId(123L);
        when(paymentNotificationService.findPaymentNotificationWithLock(1L)).thenReturn(mockNotification);
        when(paymentService.findPaymentByIdWithLock(1L)).thenReturn(mockPayment);
        when(adapterService.notifyPayment(any(NotifyPaymentAdapterRequest.class), anyString(), anyString())).thenReturn(mockResponse);
        when(processService.getInstitutionDebtTypeForProcess(any(), any(), any())).thenReturn(institutionDebtTypeDTO);
        ProcessExecutionInput input = new ProcessExecutionInputConcrete(EnumProcessCode.BILL_PAYMENT);
        input.getDataPack().put(ProcessDataPackKey.PAYMENT_NOTIFICATION_ID.getKey(), 1L);

        process.initProcess(input, new ProcessLogDTO("processLogDto"));
        process.executeProcess();

        NotifyPaymentProcessOutput output = (NotifyPaymentProcessOutput) process.getExecutionOutput();
        assertNotNull(output);
        assertEquals(BillPaymentsConsts.RESPONSE_STATUS.SUCCESS, output.getResult());
    }

    @Test
    void testExecuteProcessPaymentNotificationNotFound() throws BillException {
        SpringUtil springUtil = new SpringUtil();
        springUtil.setApplicationContext(applicationContext);

        when(getBean(ProcessService.class)).thenReturn(processService);
        when(getBean(InstitutionFeatureService.class)).thenReturn(institutionFeatureService);
        when(getBean(PaymentNotificationService.class)).thenReturn(paymentNotificationService);
        when(getBean(PaymentService.class)).thenReturn(paymentService);
        when(getBean(AdapterService.class)).thenReturn(adapterService);

        InstitutionDebtTypeDTO institutionDebtTypeDTO = new InstitutionDebtTypeDTO();
        institutionDebtTypeDTO.setId(123L);

        when(paymentNotificationService.findPaymentNotificationWithLock(1L)).thenReturn(null);
        when(processService.getInstitutionDebtTypeForProcess(any(), any(), any())).thenReturn(institutionDebtTypeDTO);

        ProcessExecutionInput input = new ProcessExecutionInputConcrete(EnumProcessCode.BILL_PAYMENT);
        input.getDataPack().put(ProcessDataPackKey.PAYMENT_NOTIFICATION_ID.getKey(), 1L);

        process.initProcess(input, new ProcessLogDTO("processLog"));
        process.executeProcess();

        NotifyPaymentProcessOutput output = (NotifyPaymentProcessOutput) process.getExecutionOutput();
        assertNotNull(output);
        assertEquals(EnumBillResult.BILL_PAYMENT_NOTIFICATION_NOT_FOUND, output.getResult());
    }

    private class ProcessExecutionInputConcrete extends ProcessExecutionInput {

        public ProcessExecutionInputConcrete(EnumProcessCode processCode) {
            super(processCode);
        }
    }
}
