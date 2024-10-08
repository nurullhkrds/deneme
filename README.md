SELECT 
    URUN AS PRODUCT_NAME, 
    COUNT(*) AS ORDER_COUNT
FROM 
    OTOLIVE.T_OTO_ABONE
WHERE 
    MUSTERINO = :CIF_NO  -- :CIF_NO yerine istekte gelen CIF_NO değeri gelecek
    AND STATU = 'A'      -- Sadece aktif kayıtları getir
GROUP BY 
    URUN;  -- Ürünlere göre gruplama
