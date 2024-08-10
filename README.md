import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

import java.time.LocalTime;

public class AbstractProcessTest {

    private AbstractProcess process;

    @BeforeEach
    public void setUp() {
        process = new NotifyPaymentProcess(); // Concrete class implementation

        // Mock nesneleri tanımla
        process.processService = mock(ProcessService.class);
        process.processChannel = mock(ProcessChannelDTO.class);
        process.institution = mock(InstitutionDTO.class);
        process.institutionDebtType = mock(InstitutionDebtTypeDTO.class);
        process.institutionChannel = mock(InstitutionChannelDTO.class);
        process.institutionProcess = mock(InstitutionProcessDTO.class);
        process.institutionChannelProcess = mock(InstitutionChannelProcessDTO.class);
        process.logDTO = mock(ProcessLogDTO.class);

        // Mock davranışlarını ayarla
        when(process.processChannel.getIsActive()).thenReturn(true);
        when(process.processChannel.getWorkingStartTime()).thenReturn(LocalTime.of(9, 0)); // 09:00
        when(process.processChannel.getWorkingFinishTime()).thenReturn(LocalTime.of(17, 0)); // 17:00
        when(process.institution.getIsActive()).thenReturn(true);
        when(process.institutionProcess.getIsActive()).thenReturn(true);
        when(process.institutionChannel.getIsActive()).thenReturn(true);
        when(process.institutionChannelProcess.getIsActive()).thenReturn(true);
        when(process.institutionDebtType.getIsActive()).thenReturn(true);
        when(process.logDTO.getResponseData1()).thenReturn(""); // responseData1'in null olmadığından emin olun
    }

    @Test
    public void testBeforeExecuteProcess_Success() throws BillException {
        // Çalışma saatleri içinde olduğundan emin olun
        when(LocalTime.now()).thenReturn(LocalTime.of(10, 0)); // 10:00

        process.beforeExecuteProcess();
    }

    @Test
    public void testAfterExecuteProcess_Success() throws BillException {
        process.executionOutput = mock(ProcessExecutionOutput.class);
        process.error = EnumBillResult.SUCCESS;

        process.afterExecuteProcess();

        assertEquals(EnumBillResult.SUCCESS.getCode().toString(), process.logDTO.getResultCode());
        assertEquals(EnumBillResult.SUCCESS.getExplanation(), process.logDTO.getResultText());
        assertEquals(EnumLoggingResultType.SUCCESS.getExplanation(), process.logDTO.getReturnType());
    }

    @Test
    public void testAfterExecuteProcess_ErrorAndRaiseException() {
        process.executionOutput = mock(ProcessExecutionOutput.class);
        process.logDTO = mock(ProcessLogDTO.class);
        process.error = EnumBillResult.SOME_ERROR; // Hata durumunu simüle edin
        process.shouldRaiseExceptionOnABillError = true;

        assertThrows(BillException.class, () -> {
            process.afterExecuteProcess();
        });
    }

    @Test
    public void testAfterExecuteProcess_ErrorNoRaiseException() throws BillException {
        process.executionOutput = mock(ProcessExecutionOutput.class);
        process.logDTO = mock(ProcessLogDTO.class);
        process.error = EnumBillResult.PAID_BILL_NOT_FOUND_ERROR; // Hata durumunu simüle edin
        process.shouldRaiseExceptionOnABillError = false;

        process.afterExecuteProcess();

        assertEquals(EnumBillResult.PAID_BILL_NOT_FOUND_ERROR.getCode().toString(), process.logDTO.getResultCode());
        assertEquals(EnumBillResult.PAID_BILL_NOT_FOUND_ERROR.getExplanation(), process.logDTO.getResultText());
        assertEquals(EnumLoggingResultType.ERROR.getExplanation(), process.logDTO.getReturnType());
    }
}
