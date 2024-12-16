UPDATE syspar.hmn_inf_named_sql_queries
SET QUERY_DEFINITION = '
    SELECT
        pslog.PRODUCT AS PRODUCT,
        pslog.INSTITUTION AS INSTITUTION,
        pslog.LOG_DATE || '' '' || pslog.LOG_TIME AS LOG_DATE,
        pslog.DP_TRANSACTION_ID AS LOG_RECORD_NO,
        pslog.SUBSCRIBER_NO AS SUBSCRIBER_NO,
        pslog.RESULT_CODE AS INSTITUTION_RETURN_CODE,
        prm.INSTITUTION_RETURN_TEXT AS INSTITUTION_RETURN_TEXT,
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
        AND pslog.PRODUCT = prm.PRODUCT
    JOIN
        t_oto_fatura t_fatura
        ON pslog.SUBSCRIBER_NO = t_fatura.ABONENO
        AND pslog.PRODUCT = t_fatura.URUN
        AND pslog.INSTITUTION = t_fatura.KURUM
    WHERE
        pslog.INSTITUTION = :P_INSTITUTION
        AND pslog.PRODUCT = :P_PRODUCT
        AND pslog.PROCESS_CODE = ''NOTIFY_PAYMENT''
        AND TRUNC(TO_DATE(pslog.LOG_DATE, ''DD.MM.YYYY'')) BETWEEN :P_START_DATE AND :P_END_DATE
        @dynamic[ P_ODMTARIH, AND TRUNC(TO_DATE(t_fatura.ODMTARIH, ''DD.MM.YYYY'')) = :P_ODMTARIH ]
        @dynamic[ P_BILL_NO, AND t_fatura.FATURANO = :P_BILL_NO ]
        @dynamic[ P_SUBSCRIBER_LIST, AND pslog.SUBSCRIBER_NO IN (:P_SUBSCRIBER_LIST) ]
        AND t_fatura.ODMTIP IN (:P_ODMTIP_LIST);
'
WHERE QUERY_NAME = 'GET_PAID_ONLINE_SERVICE_LOG_WITH_PAYMENT_DETAIL';
