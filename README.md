
    @Test
     void testBeforeExecuteProcess_Success() throws BillException {
        // Her şeyin doğru ayarlandığı durumda başarıyla tamamlanmalı
        process.beforeExecuteProcess();
    }com.ykb.payments.bill.common.exception.BillException: Transaction can be made beetween 09:00:00 and 17:00:00 for the given  channel: someChannelCode
