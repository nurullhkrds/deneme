import static org.mockito.Mockito.*;
import static org.junit.jupiter.api.Assertions.*;

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.MockitoAnnotations;

import java.time.LocalTime;

public class AbstractProcessTest {

    @InjectMocks
    private NotifyPaymentProcess process; // Somut sınıfı kullanıyoruz

    @Mock
    private ProcessService processService;
    @Mock
    private ProcessChannelDTO processChannelDTO;
    @Mock
    private InstitutionDTO institutionDTO;
    @Mock
    private InstitutionDebtTypeDTO institutionDebtTypeDTO;
    @Mock
    private InstitutionChannelDTO institutionChannelDTO;
    @Mock
    private InstitutionProcessDTO institutionProcessDTO;
    @Mock
    private InstitutionChannelProcessDTO institutionChannelProcessDTO;
    @Mock
    private ProcessLogDTO processLogDTO;
    @Mock
    private ProcessExecutionOutput executionOutput;

    @BeforeEach
    public void setUp() {
        MockitoAnnotations.openMocks(this);

        process.processService = processService;
        process.processChannel = processChannelDTO;
        process.institution = institutionDTO;
        process.institutionDebtType = institutionDebtTypeDTO;
        process.institutionChannel = institutionChannelDTO;
        process.institutionProcess = institutionProcessDTO;
        process.institutionChannelProcess = institutionChannelProcessDTO;
        process.logDTO = processLogDTO;

        when(process.processChannel.getIsActive()).thenReturn(true);
        when(process.processChannel.getWorkingStartTime()).thenReturn(LocalTime.of(9, 0));
        when(process.processChannel.getWorkingFinishTime()).thenReturn(LocalTime.of(17, 0));
        when(process.institution.getIsActive()).thenReturn(true);
        when(process.institutionProcess.getIsActive()).thenReturn(true);
        when(process.institutionChannel.getIsActive()).thenReturn(true);
        when(process.institutionChannel.getWorkingStartTime()).thenReturn(LocalTime.of(9, 0));
        when(process.institutionChannel.getWorkingFinishTime()).thenReturn(LocalTime.of(17, 0));
        when(process.institutionChannelProcess.getIsActive()).thenReturn(true);
        when(process.institutionChannelProcess.getWorkingStartTime()).thenReturn(LocalTime.of(9, 0));
        when(process.institutionChannelProcess.getWorkingFinishTime()).thenReturn(LocalTime.of(17, 0));
        when(process.institutionDebtType.getIsActive()).thenReturn(true);
        when(process.logDTO.getResponseData1()).thenReturn("");
    }

    @Test
    void testBeforeExecuteProcess_InstitutionChannelNotFound() {
        process.institutionChannel = null;

        BillException exception = assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });

        assertEquals(EnumBillResult.INSTITUTION_CHANNEL_NOT_FOUND.getCode(), exception.getResultCode());
    }

    @Test
    void testBeforeExecuteProcess_InstitutionChannelNotActive() {
        when(process.institutionChannel.getIsActive()).thenReturn(false);

        BillException exception = assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });

        assertEquals(EnumBillResult.INSTITUTION_CHANNEL_NOT_ACTIVE.getCode(), exception.getResultCode());
    }

    @Test
    void testBeforeExecuteProcess_InstitutionChannelWorkingTimeError() {
        when(process.institutionChannel.getWorkingStartTime()).thenReturn(LocalTime.of(8, 0));
        when(process.institutionChannel.getWorkingFinishTime()).thenReturn(LocalTime.of(16, 0));

        BillException exception = assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });

        assertEquals(EnumBillResult.INSTITUTION_WORKING_TIME_ERROR.getCode(), exception.getResultCode());
    }

    @Test
    void testBeforeExecuteProcess_InstitutionDebtTypeNotFound() {
        process.institutionDebtType = null;

        BillException exception = assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });

        assertEquals(EnumBillResult.INSTITUTION_DEBT_TYPE_NOT_FOUND.getCode(), exception.getResultCode());
    }

    @Test
    void testBeforeExecuteProcess_InstitutionDebtTypeNotActive() {
        when(process.institutionDebtType.getIsActive()).thenReturn(false);

        BillException exception = assertThrows(BillException.class, () -> {
            process.beforeExecuteProcess();
        });

        assertEquals(EnumBillResult.INSTITUTION_DEBT_TYPE_NOT_ACTIVE.getCode(), exception.getResultCode());
    }

    @Test
    void testAfterExecuteProcess_Success() throws BillException {
        when(executionOutput.toString()).thenReturn("executionOutput");
        process.executionOutput = executionOutput;
        process.error = null;  // No error

        process.afterExecuteProcess();

        verify(process.logDTO).setResponseData1(contains("executionOutput"));
        verify(process.logDTO).setResultCode(EnumBillResult.SUCCESS.getCode().toString());
        verify(process.logDTO).setResultText(EnumBillResult.SUCCESS.getExplanation());
        verify(process.logDTO).setReturnType(EnumLoggingResultType.SUCCESS.getExplanation());
    }

    @Test
    void testAfterExecuteProcess_WithErrorAndRaiseException() {
        process.error = EnumBillResult.INSTITUTION_CHANNEL_NOT_FOUND;  // An example error
        process.shouldRaiseExceptionOnABillError = true;

        BillException exception = assertThrows(BillException.class, () -> {
            process.afterExecuteProcess();
        });

        assertEquals(EnumBillResult.INSTITUTION_CHANNEL_NOT_FOUND.getCode(), exception.getResultCode());
    }

    @Test
    void testAfterExecuteProcess_WithErrorNoRaiseException() throws BillException {
        when(executionOutput.toString()).thenReturn("executionOutput");
        process.executionOutput = executionOutput;
        process.error = EnumBillResult.INSTITUTION_CHANNEL_NOT_FOUND;  // An example error
        process.shouldRaiseExceptionOnABillError = false;

        process.afterExecuteProcess();

        verify(process.logDTO).setResponseData1(contains("executionOutput"));
        verify(process.logDTO).setResultCode(EnumBillResult.INSTITUTION_CHANNEL_NOT_FOUND.getCode().toString());
        verify(process.logDTO).setResultText(EnumBillResult.INSTITUTION_CHANNEL_NOT_FOUND.getExplanation());
        verify(process.logDTO).setReturnType(EnumLoggingResultType.ERROR.getExplanation());
    }
}
