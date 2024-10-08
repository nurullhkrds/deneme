SELECT 
    MUSTERINO
FROM 
    OTOLIVE.T_OTO_ABONE
WHERE 
    URUN = 'CEPTEL'
    AND STATU = 'A'  -- Sadece aktif kayıtlar
GROUP BY 
    MUSTERINO
HAVING 
    COUNT(*) > 8;  -- 8'den fazla talimatı olan müşteriler
