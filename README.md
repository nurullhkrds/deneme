INSERT INTO QUERY_METADATA (
    QUERY_NAME, 
    DATASOURCE, 
    QUERY_DEFINITION, 
    COMPONENT_NAME, 
    TYPE
) VALUES (
    'GET_PRODUCT_DETAIL_LIST_BY_CIF_NO_AND_PRODUCT',
    'CBS',
    'SELECT *
    FROM (
        SELECT
            CASE
                WHEN URUN IN (''CEPTEL'', ''ELEKTRİK'', ''SU'', ''DOĞALGAZ'', ''TELEKOM'') THEN URUN
                WHEN :PRODUCT = ''NONE'' AND URUN IN (''SİGORTA'', ''DBS'', ''SGK'', ''SPT'', ''HAVALE'', ''KKNTS'', ''TEDANAFRM'', ''KOÇFİNANS'', ''SSK'', ''TAHSİLAT'', ''KREDİLER'') THEN URUN
            END AS PRODUCT,
            KURUM AS INSTITUTION,
            ABONENO AS SUBSCRIBER_NO,
            GIRISTARIH AS ENTRY_DATE,
            CASE
                WHEN URUN IN (''CEPTEL'', ''ELEKTRİK'', ''SU'', ''DOĞALGAZ'', ''TELEKOM'') THEN ''TRUE''
                WHEN :PRODUCT = ''NONE'' AND URUN IN (''SİGORTA'', ''DBS'', ''SGK'', ''SPT'', ''HAVALE'', ''KKNTS'', ''TEDANAFRM'', ''KOÇFİNANS'', ''SSK'', ''TAHSİLAT'', ''KREDİLER'') THEN ''FALSE''
                ELSE ''FALSE''
            END AS REVERSIBLE
        FROM
            OTOLIVE.T_OTO_ABONE
        WHERE
            MUSTERINO = :CIF_NO
            AND (
                (:PRODUCT IN (''CEPTEL'', ''ELEKTRİK'', ''SU'', ''DOĞALGAZ'', ''TELEKOM'') AND URUN = :PRODUCT)
                OR (:PRODUCT = ''NONE'' AND URUN IN (''SİGORTA'', ''DBS'', ''SGK'', ''SPT'', ''HAVALE'', ''KKNTS'', ''TEDANAFRM'', ''KOÇFİNANS'', ''SSK'', ''TAHSİLAT'', ''KREDİLER''))
            )
            AND STATU = ''A''
        ORDER BY GIRISTARIH ASC
    )
    WHERE ROWNUM <= 8',
    'HMN_PYM_BillPayments',
    0
);
