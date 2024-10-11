SELECT
    CASE
        WHEN URUN IN ('CEPTEL', 'ELEKTRİK', 'SU', 'DOĞALGAZ') THEN URUN
        ELSE 'OTHERS'
    END AS PRODUCT,
    COUNT(*) AS ORDER_COUNT
FROM
    OTOLIVE.T_OTO_ABONE
WHERE
    MUSTERINO = :CIF_NO
    AND STATU = 'A'
GROUP BY
    CASE
        WHEN URUN IN ('CEPTEL', 'ELEKTRİK', 'SU', 'DOĞALGAZ') THEN URUN
        ELSE 'OTHERS'
    END





('CEPTEL', 'ELEKTRİK', 'SU', 'DOĞALGAZ') bu ürünler ise direk bu ürün isimleriyle ,

TELEKOM,TAHSİLAT,KREDİLER ürünleri ise other adı altında 

ve bu diğer tüm ürünler dışındakileri ise none 'NONE' diye adlandır 
