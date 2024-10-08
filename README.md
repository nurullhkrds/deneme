WITH OrderedResults AS (
    SELECT 
        CASE 
            -- Eğer URUN CEPTEL, ELEKTRİK, SU, DOĞALGAZ ise bu ürün adı PRODUCT olarak döner
            WHEN URUN IN ('CEPTEL', 'ELEKTRİK', 'SU', 'DOĞALGAZ') THEN URUN
            -- Eğer PRODUCT OTHERS ise PRODUCT = 'OTHERS - <URUN>' olarak döner
            WHEN :PRODUCT = 'OTHERS' THEN 'OTHERS - ' || URUN
        END AS PRODUCT,
        KURUM AS INSTITUTION,
        ABONENO AS SUBSCRIBER_NO,
        GIRISTARIH AS ENTRY_DATE,
        CASE
            -- Eğer PRODUCT CEPTEL, ELEKTRİK, SU, DOĞALGAZ ise REVERSIBLE = TRUE
            WHEN URUN IN ('CEPTEL', 'ELEKTRİK', 'SU', 'DOĞALGAZ') THEN 'TRUE'
            -- Eğer PRODUCT = 'OTHERS' ve URUN TELEKOM, TAHSİLAT, KREDİLER ise REVERSIBLE = TRUE
            WHEN :PRODUCT = 'OTHERS' AND URUN IN ('TELEKOM', 'TAHSİLAT', 'KREDİLER') THEN 'TRUE'
            -- Eğer PRODUCT = 'OTHERS' ve URUN SİGORTA, DBS vb. ise REVERSIBLE = FALSE
            WHEN :PRODUCT = 'OTHERS' AND URUN IN ('SİGORTA', 'DBS', 'SGK', 'SPT', 'HAVALE', 'KKNTS', 'TEDANAFRM', 'KOÇFİNANS', 'SSK') THEN 'FALSE'
            -- Diğer durumlarda REVERSIBLE = FALSE
            ELSE 'FALSE'
        END AS REVERSIBLE,
        ROW_NUMBER() OVER (PARTITION BY URUN ORDER BY GIRISTARIH ASC) AS RowNum
    FROM 
        OTOLIVE.T_OTO_ABONE
    WHERE 
        MUSTERINO = :CIF_NO  -- CIF_NO dışarıdan input olarak gelecek
        AND (
            -- Eğer PRODUCT CEPTEL, ELEKTRİK, SU, DOĞALGAZ ise URUN bu değerlerden biri olacak
            (:PRODUCT IN ('CEPTEL', 'ELEKTRİK', 'SU', 'DOĞALGAZ') AND URUN = :PRODUCT) 
            -- Eğer PRODUCT OTHERS ise URUN OTHERS kategorisindeki bir ürün olacak
            OR (:PRODUCT = 'OTHERS' AND URUN IN ('TELEKOM', 'TAHSİLAT', 'KREDİLER', 'SİGORTA', 'DBS', 'SGK', 'SPT', 'HAVALE', 'KKNTS', 'TEDANAFRM', 'KOÇFİNANS', 'SSK'))
        )
        AND STATU = 'A'  -- Sadece aktif kayıtlar getirilecek
)
SELECT * FROM OrderedResults
WHERE RowNum <= 8;  -- Her ürün grubu için maksimum 8 kayıt döner
