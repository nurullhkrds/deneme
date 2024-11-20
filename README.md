INSERT INTO QUERY_METADATA (
    QUERY_NAME, 
    DATASOURCE, 
    QUERY_DEFINITION, 
    COMPONENT_NAME, 
    TYPE
) VALUES (
    'GET_PRODUCT_LIST_BY_CIF_NO',
    'CBS',
    'SELECT
        CASE
            WHEN URUN IN (''CEPTEL'', ''ELEKTRİK'', ''SU'', ''DOĞALGAZ'', ''TELEKOM'') THEN URUN
            ELSE ''NONE''
        END AS PRODUCT,
        COUNT(*) AS ORDER_COUNT
     FROM
        OTOLIVE.T_OTO_ABONE
     WHERE
        MUSTERINO = :CIF_NO
        AND STATU = ''A''
     GROUP BY
        CASE
            WHEN URUN IN (''CEPTEL'', ''ELEKTRİK'', ''SU'', ''DOĞALGAZ'', ''TELEKOM'') THEN URUN
            ELSE ''NONE''
        END',
    'HMN_PYM_BillPayments',
    0
);
