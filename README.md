SELECT 
    MUSTERINO
FROM 
    OTOLIVE.T_OTO_ABONE
WHERE 
    URUN IN ('SGK', 'SİGORTA')  -- SGK ve SİGORTA ürünlerini kontrol ediyoruz
    AND STATU = 'A'  -- Sadece aktif kayıtları alıyoruz
GROUP BY 
    MUSTERINO
HAVING 
    COUNT(DISTINCT URUN) = 2  -- Hem SGK hem de SİGORTA olmalı
