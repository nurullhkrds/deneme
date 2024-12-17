        
        
        
    SELECT
    t_fatura.URUN AS PRODUCT,
    t_fatura.ABONENO AS SUBSCRIBER_NO,
    t_fatura.KURUM AS INSTITUTION,
    t_fatura.FATURANO AS BILL_NO,
    t_fatura.ODMTIP AS PAYMENT_TYPE,
    t_fatura.ODMTARIH AS PAYMENT_DATE
FROM
    t_oto_fatura t_fatura
WHERE
    t_fatura.KURUM = :P_INSTITUTION
    AND t_fatura.URUN = :P_PRODUCT 
    AND t_fatura.ODMTIP IN (:P_ODMTIP_LIST) 
    @dynamic[ P_BILL_NO, AND t_fatura.FATURANO = :P_BILL_NO ]
    @dynamic[ P_SUBSCRIBER_LIST, AND t_fatura.ABONENO IN (:P_SUBSCRIBER_LIST) ]
    @dynamic[ P_ODMTARIH, AND t_fatura.ODMTARIH = TO_DATE(:P_ODMTARIH, 'DD.MM.YYYY')]
