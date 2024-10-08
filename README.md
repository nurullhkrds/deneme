SELECT *
FROM (
    SELECT 
        CASE 
            WHEN URUN IN ('CEPTEL', 'ELEKTRİK', 'SU', 'DOĞALGAZ') THEN URUN
            WHEN :PRODUCT = 'OTHERS' THEN 'OTHERS - ' || URUN
            ELSE URUN
        END AS PRODUCT,
        KURUM AS INSTITUTION,
        ABONENO AS SUBSCRIBER_NO,
        GIRISTARIH AS ENTRY_DATE,
        CASE
            WHEN URUN IN ('CEPTEL', 'ELEKTRİK', 'SU', 'DOĞALGAZ') THEN 'TRUE'
            WHEN :PRODUCT = 'OTHERS' AND URUN IN ('TELEKOM', 'TAHSİLAT', 'KREDİLER') THEN 'TRUE'
            WHEN :PRODUCT = 'OTHERS' AND URUN IN ('SİGORTA', 'DBS', 'SGK', 'SPT', 'HAVALE', 'KKNTS', 'TEDANAFRM', 'KOÇFİNANS', 'SSK') THEN 'FALSE'
            ELSE 'FALSE'
        END AS REVERSIBLE,
        ROW_NUMBER() OVER (PARTITION BY URUN ORDER BY GIRISTARIH ASC) AS RowNum
    FROM 
        OTOLIVE.T_OTO_ABONE
    WHERE 
        MUSTERINO = :CIF_NO  -- Müşteri numarası dışarıdan gelecek
        AND (
            (:PRODUCT IN ('CEPTEL', 'ELEKTRİK', 'SU', 'DOĞALGAZ') AND URUN = :PRODUCT) 
            OR (:PRODUCT = 'OTHERS' AND URUN IN ('TELEKOM', 'TAHSİLAT', 'KREDİLER', 'SİGORTA', 'DBS', 'SGK', 'SPT', 'HAVALE', 'KKNTS', 'TEDANAFRM', 'KOÇFİNANS', 'SSK'))
        )
        AND STATU = 'A'  -- Sadece aktif kayıtlar getirilecek
) AS OrderedResults
WHERE RowNum <= 8;
