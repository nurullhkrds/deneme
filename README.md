

SELECT 
    pslog.PRODUCT AS PRODUCT,                               -- Ürün Bilgisi
    pslog.INSTITUTION AS INSTITUTION,                       -- Kurum Bilgisi
    pslog.LOG_DATE || ' ' || pslog.LOG_TIME AS LOG_DATE,    -- Log Tarihi
    pslog.DP_TRANSACTION_ID || ' ' || PSLOG.ADDITIONAL_INFO AS LOG_RECORD_NO, -- Log Kayıt Numarası
    pslog.SUBSCRIBER_NO AS SUBSCRIBER_NO,                   -- Abone Numarası
    t_fatura.FATURANO AS BILL_NO,                           -- Fatura Numarası
    t_fatura.TUTAR AS TOTAL_AMOUNT,                         -- Fatura Tutarı
    t_fatura.ODNTUTAR AS PAID_AMOUNT,                       -- Ödenen Tutar
    t_fatura.SONODMTARIH AS DUE_DATE,                       -- Son Ödeme Tarihi
    t_fatura.ODMTARIH AS PAYMENT_DATE,                      -- Ödeme Tarihi
    t_fatura.ODMTIP AS PAYMENT_TYPE,                        -- Ödeme Tipi
    t_fatura.REFERANS AS REFERENCE_NO                       -- Referans Numarası
FROM 
    PYM_ONLINE_SERVICE_LOG pslog
JOIN 
    PYM_ONLINE_RETURN_MAP prm 
    ON pslog.RESULT_CODE = prm.INSTITUTION_RETURN_CODE 
    AND pslog.INSTITUTION = prm.institution
JOIN 
    t_oto_fatura t_fatura 
    ON pslog.SUBSCRIBER_NO = t_fatura.ABONENO 
    AND pslog.PRODUCT = t_fatura.URUN
    AND pslog.INSTITUTION = t_fatura.KURUM
WHERE 
    pslog.INSTITUTION = 'ARMADAŞ' -- Kurum Filtresi
    AND pslog.PRODUCT = 'DOĞALGAZ' -- Ürün Filtresi
    AND pslog.SUBSCRIBER_NO IN ('0010250422','0010224298') -- Abone No Filtresi
    AND pslog.PROCESS_CODE LIKE '%NOTIFY_PAYMENT%' -- Ödeme Bildirimi Filtresi
    AND pslog.LOG_DATE BETWEEN TO_DATE('09.12.2024', 'DD.MM.YYYY') AND TO_DATE('10.12.2024', 'DD.MM.YYYY')  -- Bildirim Tarih Filtresi
    AND pslog.ERROR = 'H18' -- Hata Filtresi
    AND t_fatura.ODMTARIH = TO_DATE('09.12.2024', 'DD.MM.YYYY') -- Ödeme Tarihi Filtresi
    AND t_fatura.ODMTIP IN ('I','V'); -- Ödeme Tipi Filtresi
