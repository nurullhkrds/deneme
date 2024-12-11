SELECT 
          pslog.PRODUCT AS PRODUCT,
    pslog.INSTITUTION AS INSTITUTION,
    pslog.LOG_DATE || ' ' || pslog.LOG_TIME AS LOG_DATE,
    pslog.DP_TRANSACTION_ID AS LOG_RECORD_NO,
    pslog.SUBSCRIBER_NO AS SUBSCRIBER_NO,
    pslog.result_code AS INSTITUTION_RETURN_CODE,
    prm.institution_return_text AS INSTITUTION_RETURN_TEXT,
    t_fatura.FATURANO AS BILL_NO,
    t_fatura.TUTAR AS TOTAL_AMOUNT,
    t_fatura.ODNTUTAR AS PAID_AMOUNT,
    t_fatura.SONODMTARIH AS DUE_DATE,
    t_fatura.ODMTARIH AS PAYMENT_DATE,
    t_fatura.ODMTIP AS PAYMENT_TYPE,
    t_fatura.REFERANS AS REFERENCE_NO 
FROM 
    PYM_ONLINE_SERVICE_LOG pslog
JOIN 
    PYM_ONLINE_RETURN_MAP prm 
    ON pslog.RESULT_CODE = prm.INSTITUTION_RETURN_CODE 
    AND pslog.INSTITUTION = prm.INSTITUTION
    and pslog.product = prm.product
JOIN 
    t_oto_fatura t_fatura 
    ON pslog.SUBSCRIBER_NO = t_fatura.ABONENO 
    AND pslog.PRODUCT = t_fatura.URUN
    AND pslog.INSTITUTION = t_fatura.KURUM
WHERE 
    pslog.INSTITUTION = :P_INSTITUTION -- Kurum Filtresi
    AND pslog.PRODUCT = :P_PRODUCT -- Ürün Filtresi
    AND pslog.SUBSCRIBER_NO IN (:P_SUBSCRIBER_LIST) -- Abone No Filtresi
    AND pslog.PROCESS_CODE = :P_PROCESS_CODE -- Ödeme Bildirimi Filtresi
    AND pslog.LOG_DATE BETWEEN :P_START_DATE AND :P_END_DATE -- Bildirim Tarih Filtresi
    AND pslog.ERROR = :P_ERROR -- Hata Filtresi
    AND t_fatura.ODMTARIH = :P_ODMTARIH -- Ödeme Tarihi Filtresi
    AND t_fatura.ODMTIP IN (:P_ODMTIP_LIST); -- Ödeme Tipi Filtresi
