import org.junit.jupiter.api.Test;
import java.lang.reflect.Constructor;
import java.lang.reflect.Method;
import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.Mockito.*;

public class NotifyPaymentProcessTest {

    @Test
    public void testGatherDataExecuteStep() throws Exception {
        // Mock dependencies
        PaymentService paymentServiceMock = mock(PaymentService.class);
        AdapterService adapterServiceMock = mock(AdapterService.class);
        PaymentNotificationService paymentNotificationServiceMock = mock(PaymentNotificationService.class);

        // Use SpringUtil to return mocks
        when(SpringUtil.getBean(PaymentService.class)).thenReturn(paymentServiceMock);
        when(SpringUtil.getBean(AdapterService.class)).thenReturn(adapterServiceMock);
        when(SpringUtil.getBean(PaymentNotificationService.class)).thenReturn(paymentNotificationServiceMock);

        // Mock feature service response
        InstitutionFeatureService featureServiceMock = mock(InstitutionFeatureService.class);
        when(featureServiceMock.getFeatureValue(any(), any(), any())).thenReturn("5");
        when(SpringUtil.getBean(InstitutionFeatureService.class)).thenReturn(featureServiceMock);

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

        // Sonuçları kontrol edin (örneğin, notificationTryCount değeri)
        assertEquals(5L, process.notificationTryCount);
        assertEquals(123L, process.paymentNotificationId);
    }
}
