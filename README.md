SELECT CASE
         WHEN EXISTS (
              SELECT 1
                FROM OTOLIVE.T_OTO_KURUM k
               WHERE k.urun  = :URUN
                 AND k.kurum = :KURUM
            )
          AND NOT EXISTS (
              SELECT 1
                FROM OTOLIVE.T_OTO_KURUMDETAY kd
               WHERE kd.urun  = :URUN
                 AND kd.kurum = :KURUM
                 AND kd.aktif = 'A'
            )
         THEN 1
         ELSE 0
       END AS IS_CONVERT_ELIGIBLE
FROM dual;
