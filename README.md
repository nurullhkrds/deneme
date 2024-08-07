import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.MockedStatic;
import org.mockito.Mockito;
import org.springframework.context.ApplicationContext;

import java.lang.reflect.Constructor;
import java.lang.reflect.Field;
import java.lang.reflect.Method;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.Mockito.*;

public class NotifyPaymentProcessTest {

    private ApplicationContext mockAppContext;

    @BeforeEach
    public void setup() {
        // Mock ApplicationContext
        mockAppContext = mock(ApplicationContext.class);

        // Mock SpringUtil's static method getBean
        MockedStatic<SpringUtil> mockedSpringUtil = Mockito.mockStatic(SpringUtil.class);

        // Return the mocked ApplicationContext when SpringUtil's appContext is accessed
        mockedSpringUtil.when(SpringUtil::getAppContext).thenReturn(mockAppContext);
    }

    @Test
    public void testGatherDataExecuteStep() throws Exception {
        // Mock dependencies
        PaymentService paymentServiceMock = mock(PaymentService.class);
        AdapterService adapterServiceMock = mock(AdapterService.class);
        PaymentNotificationService paymentNotificationServiceMock = mock(PaymentNotificationService.class);

        // Use ApplicationContext to return mocks
        when(mockAppContext.getBean(PaymentService.class)).thenReturn(paymentServiceMock);
        when(mockAppContext.getBean(AdapterService.class)).thenReturn(adapterServiceMock);
        when(mockAppContext.getBean(PaymentNotificationService.class)).thenReturn(paymentNotificationServiceMock);

        // Mock feature service response
        InstitutionFeatureService featureServiceMock = mock(InstitutionFeatureService.class);
        when(featureServiceMock.getFeatureValue(any(), any(), any())).thenReturn("5");
        when(mockAppContext.getBean(InstitutionFeatureService.class)).thenReturn(featureServiceMock);

        NotifyPaymentProcess process = new NotifyPaymentProcess();

        // Prepare dataPack mock
        process.dataPack = mock(Map.class);
        when(process.dataPack.get(ProcessDataPackKey.PAYMENT_NOTIFICATION_ID.getKey())).thenReturn(123L);

        // Inner class'ın Class objesini alın
        Class<?> innerClass = Class.forName("com.yourpackage.NotifyPaymentProcess$GatherData");

        // Inner class'ın constructor'ını alın
        Constructor<?> ctor = innerClass.getDeclaredConstructor(NotifyPaymentProcess.class);
        ctor.setAccessible(true);

        // Inner class instance'ını oluşturun
        Object innerObject = ctor.newInstance(process);

        // Test etmek istediğiniz metodu alın
        Method executeStepMethod = innerClass.getDeclaredMethod("executeStep");
        executeStepMethod.setAccessible(true);

        // Metodu çağırın
        executeStepMethod.invoke(innerObject);

        // Private alanlara erişim
        Field notificationTryCountField = NotifyPaymentProcess.class.getDeclaredField("notificationTryCount");
        notificationTryCountField.setAccessible(true);
        Long notificationTryCount = (Long) notificationTryCountField.get(process);

        Field paymentNotificationIdField = NotifyPaymentProcess.class.getDeclaredField("paymentNotificationId");
        paymentNotificationIdField.setAccessible(true);
        Long paymentNotificationId = (Long) paymentNotificationIdField.get(process);

        // Sonuçları kontrol edin
        assertEquals(5L, notificationTryCount);
        assertEquals(123L, paymentNotificationId);
    }
}
