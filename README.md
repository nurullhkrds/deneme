SELECT 
    pslog.SUBSCRIBER_NO AS Abone_No,
    pslog.LOG_DATE AS Log_Tarihi,
    pslog.RESULT_CODE AS Hata_Kodu,
    prm.INSTITUTION_RETURN_TEXT AS Kurum_Hata_Mesajı,
    t_fatura.FATURANO AS Fatura_No,
    t_fatura.TUTAR AS Fatura_Tutarı,
    t_fatura.ODNTUTAR AS Ödenen_Tutar,
    t_fatura.SONODMTARIH AS Son_Ödeme_Tarihi,
    t_fatura.ODMTARIH AS Ödeme_Tarihi
FROM 
    PYM_ONLINE_SERVICE_LOG pslog
JOIN 
    PYM_ONLINE_RETURN_MAP prm 
    ON pslog.RESULT_CODE = prm.INSTITUTION_RETURN_CODE 
    AND pslog.INSTITUTION = prm.INSTITUTION
JOIN 
    t_oto_fatura t_fatura 
    ON pslog.SUBSCRIBER_NO = t_fatura.ABONENO 
    AND pslog.PRODUCT = t_fatura.URUN
    AND pslog.INSTITUTION = t_fatura.KURUM
WHERE 
    pslog.INSTITUTION = 'İZMİRGAZ' -- Kurum Filtresi
    AND pslog.PRODUCT = 'DOĞALGAZ' -- Ürün Filtresi
    AND pslog.SUBSCRIBER_NO IN ('3866030000', '1562', '1342', '232', '32132', '12312312', '123123') -- Abone No Filtresi
    AND pslog.PROCESS_CODE LIKE '%NOTIFY%' -- Ödeme Bildirimi Filtresi
    AND pslog.LOG_DATE BETWEEN '2024-08-21' AND '2024-08-23' -- Bildirim Tarih Filtresi
    AND pslog.ERROR != '00000' -- Hata Filtresi
    AND t_fatura.ODMTARIH = '2024-08-21'; -- Ödeme Tarihi Filtresi










SELECT 
    pslog.SUBSCRIBER_NO AS Abone_No,
    pslog.LOG_DATE AS Log_Tarihi,
    pslog.RESULT_CODE AS Hata_Kodu,
    prm.INSTITUTION_RETURN_TEXT AS Kurum_Hata_Mesajı,
    t_fatura.FATURANO AS Fatura_No,
    t_fatura.TUTAR AS Fatura_Tutarı,
    t_fatura.ODNTUTAR AS Ödenen_Tutar,
    t_fatura.SONODMTARIH AS Son_Ödeme_Tarihi,
    t_fatura.ODMTARIH AS Ödeme_Tarihi
FROM 
    PYM_ONLINE_SERVICE_LOG pslog
JOIN 
    PYM_ONLINE_RETURN_MAP prm 
    ON pslog.RESULT_CODE = prm.INSTITUTION_RETURN_CODE 
    AND pslog.INSTITUTION = prm.INSTITUTION
JOIN 
    t_oto_fatura t_fatura 
    ON pslog.SUBSCRIBER_NO = t_fatura.ABONENO 
    AND pslog.PRODUCT = t_fatura.URUN
    AND pslog.INSTITUTION = t_fatura.KURUM
WHERE 
    pslog.INSTITUTION = :p_institution -- Kurum Filtresi
    AND pslog.PRODUCT = :p_product -- Ürün Filtresi
    AND pslog.SUBSCRIBER_NO IN (:p_subscriber_list) -- Abone No Filtresi
    AND pslog.PROCESS_CODE LIKE :p_process_code -- Ödeme Bildirimi Filtresi
    AND pslog.LOG_DATE BETWEEN :p_start_date AND :p_end_date -- Bildirim Tarih Filtresi
    AND pslog.ERROR != :p_error -- Hata Filtresi
    AND t_fatura.ODMTARIH = :p_odmtarih; -- Ödeme Tarihi Filtresi

