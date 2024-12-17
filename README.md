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
    t_fatura.KURUM = :P_INSTITUTION -- İlk koşul
    AND t_fatura.URUN = :P_PRODUCT -- Sabit ürün
    AND t_fatura.ODMTIP IN (:P_ODMTIP_LIST) -- Zorunlu ödeme tipi
    @dynamic[ P_BILL_NO, AND t_fatura.FATURANO = :P_BILL_NO ]
    @dynamic[ P_SUBSCRIBER_NO, AND t_fatura.ABONENO IN (:P_SUBSCRIBER_NO) ]
    @dynamic[ P_ODMTARIH, AND TRUNC(TO_DATE(t_fatura.ODMTARIH, 'DD.MM.YYYY')) = :P_ODMTARIH ];
